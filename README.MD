To my knowledge, DoubleDeuce was the Fastest 128bit hash for hashing small keys (<1024 bytes).  
The a5hash128 5.14 by Aleksey Vaneev outperformed my 128 bit hasher, it led to **crafting the FASTEST "Non-Quality" hasher under Heaven - Pippip_AES**.  
It is used in my Zennish MicroDeduplicator Nakamichi 'DoubleDeuce' to hash 16+ bytes long matches down to 16 bytes.
    
![Gumbotron_darker](https://github.com/Sanmayce/Nakamichi/assets/14062548/fb63af7b-1c25-4116-9ff4-11d6a8007a24)
    
The following code is the C code written by Kaze (sanmayce@sanmayce.com):
    
```
// https://godbolt.org/ [[[
#include <stdlib.h>
#include <stdint.h> // uint64_t needed
#include <string.h> // memset
#include <smmintrin.h> // SSE4.1 intrinsics
#include <wmmintrin.h>
#include <immintrin.h> // AVX intrinsics
//#include <zmmintrin.h> // AVX2 intrinsics, definitions and declarations for use with 512-bit compiler intrinsics.
void SlowCopy128bit (const char *SOURCE, char *TARGET) { _mm_storeu_si128((__m128i *)(TARGET), _mm_loadu_si128((const __m128i *)(SOURCE))); }
unsigned char DDAES[16];
// https://godbolt.org/ ]]]
//static const uint8_t VectorsNeedNonVAriable1[256] __attribute__((aligned(16))) =
static const uint8_t VectorsNeedNonVAriable1[256] =
{
	0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
	0xFF,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
	0xFF,0xFF,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
	0xFF,0xFF,0xFF,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
	0xFF,0xFF,0xFF,0xFF,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
	0xFF,0xFF,0xFF,0xFF,0xFF,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
	0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
	0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
	0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
	0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
	0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0x00,0x00,0x00,0x00,0x00,0x00,
	0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0x00,0x00,0x00,0x00,0x00,
	0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0x00,0x00,0x00,0x00,
	0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0x00,0x00,0x00,
	0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0x00,0x00,
	0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0x00
};
static const __m128i *Mumbotron = (__m128i *) VectorsNeedNonVAriable1;
//static const uint8_t VectorsNeedNonVAriable2[256] __attribute__((aligned(16))) =
static const uint8_t VectorsNeedNonVAriable2[256] =
{
	0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
	0x00,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
	0x00,0x00,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
	0x00,0x00,0x00,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
	0x00,0x00,0x00,0x00,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
	0x00,0x00,0x00,0x00,0x00,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
	0x00,0x00,0x00,0x00,0x00,0x00,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
	0x00,0x00,0x00,0x00,0x00,0x00,0x00,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
	0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
	0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
	0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,
	0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0xFF,0xFF,0xFF,0xFF,0xFF,
	0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0xFF,0xFF,0xFF,0xFF,
	0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0xFF,0xFF,0xFF,
	0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0xFF,0xFF,
	0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0xFF
}; 
static const __m128i *Jumbotron = (__m128i *) VectorsNeedNonVAriable2;
void DoubleDeuceAES_Gumbotron_YMM(const uint8_t *buffer, size_t length) {
	size_t i, Cycles;
	__m128i hashA = _mm_set_epi64x(0x6c62272e07bb0142, 0x62b821756295c58d); // 0x6c62272e07bb014262b821756295c58d // _mm_setzero_si128();
	__m128i hashB = _mm_set_epi64x(0xdd268dbcaac55036, 0x2d98c384c4e576cc); // 0xdd268dbcaac550362d98c384c4e576ccc8b1536847b6bbb31023b4c8caee0535 // FNV offset basis
	__m128i hashC = _mm_set_epi64x(0xc8b1536847b6bbb3, 0x1023b4c8caee0535); // 0xdd268dbcaac550362d98c384c4e576ccc8b1536847b6bbb31023b4c8caee0535 // FNV offset basis
	__m128i hashD = _mm_setzero_si128();
	__m128i a0,a1,a2,a3; // Instead of this chunkenization, ZMM houses the 4 XMMs, if there is shuffle across all the 512bits, use it. There is, but __m256i _mm256_shuffle_epi8(__m256i a, __m256i b) is more handy.
	__m256i a0YMM,a2YMM;
	__m128i b0,b1,b2,b3;
	__m256i b0YMM,b2YMM;
	__m128i c0,c1,c2,c3;
	__m256i c0YMM,c2YMM;
	__m128i d0,d1,d2,d3;
	__m256i d0YMM,d2YMM;
	__m128i tmp0,tmp1,tmp2,tmp3;
	__m256i tmp0YMM,tmp2YMM;
	__m128i ReverseMask =   _mm_set_epi8(0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15);
	__m256i ReverseMaskYMM = _mm256_set_epi8(0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31);
	__m128i PartialInterleavingMask1 = _mm_set_epi8(0x80,7,0x80,6,0x80,5,0x80,4,0x80,3,0x80,2,0x80,1,0x80,0);
	__m128i PartialInterleavingMask2 = _mm_set_epi8(0x80,0xf,0x80,0xe,0x80,0xd,0x80,0xc,0x80,0xb,0x80,0xa,0x80,9,0x80,8); 
	__m128i PartialInterleavingMask3 = _mm_set_epi8(7,0x80,6,0x80,5,0x80,4,0x80,3,0x80,2,0x80,1,0x80,0,0x80);
	__m128i PartialInterleavingMask4 = _mm_set_epi8(0xf,0x80,0xe,0x80,0xd,0x80,0xc,0x80,0xb,0x80,0xa,0x80,9,0x80,8,0x80);
	__m256i PartialInterleavingMask1YMM = _mm256_set_epi8(0x80,0xf,0x80,0xe,0x80,0xd,0x80,0xc,0x80,0xb,0x80,0xa,0x80,9,0x80,8,0x80,7,0x80,6,0x80,5,0x80,4,0x80,3,0x80,2,0x80,1,0x80,0);
//	__m256i PartialInterleavingMask2YMM = _mm256_set_epi8(0x80,0xf+16,0x80,0xe+16,0x80,0xd+16,0x80,0xc+16,0x80,0xb+16,0x80,0xa+16,0x80,9+16,0x80,8+16,0x80,7+16,0x80,6+16,0x80,5+16,0x80,4+16,0x80,3+16,0x80,2+16,0x80,1+16,0x80,0+16);
	__m256i PartialInterleavingMask3YMM = _mm256_set_epi8(0xf,0x80,0xe,0x80,0xd,0x80,0xc,0x80,0xb,0x80,0xa,0x80,9,0x80,8,0x80,7,0x80,6,0x80,5,0x80,4,0x80,3,0x80,2,0x80,1,0x80,0,0x80);
//	__m256i PartialInterleavingMask4YMM = _mm256_set_epi8(0xf+16,0x80,0xe+16,0x80,0xd+16,0x80,0xc+16,0x80,0xb+16,0x80,0xa+16,0x80,9+16,0x80,8+16,0x80,7+16,0x80,6+16,0x80,5+16,0x80,4+16,0x80,3+16,0x80,2+16,0x80,1+16,0x80,0+16,0x80);
	const __m128i *ptr128a, *ptr128b, *ptr128c, *ptr128d;

	__m128i AgainstRules, GumbotronREVER, GumbotronINTER, Gumbotron, GumbotronREVERINTER;
	const __m128i *ptr128; 
	__m128i InterleaveMask =   _mm_set_epi8(15,7,14,6,13,5,12,4,11,3,10,2,9,1,8,0);
uint8_t vector[32];

	if (length >= 64) {
		Cycles = length/64;
		for(; Cycles--; buffer += 64) {
			//a0 = _mm_loadu_si128((__m128i *)(buffer+0*16)); 
			//a1 = _mm_loadu_si128((__m128i *)(buffer+1*16)); 
			//a2 = _mm_loadu_si128((__m128i *)(buffer+2*16)); 
			//a3 = _mm_loadu_si128((__m128i *)(buffer+3*16)); 
			a0YMM = _mm256_loadu_si256((__m256i *)(buffer+0*16)); 
			a2YMM = _mm256_loadu_si256((__m256i *)(buffer+2*16)); 
			//b0 = _mm_shuffle_epi8 (a3, ReverseMask); 
			//b1 = _mm_shuffle_epi8 (a2, ReverseMask); 
			//b2 = _mm_shuffle_epi8 (a1, ReverseMask); 
			//b3 = _mm_shuffle_epi8 (a0, ReverseMask); 
			b0YMM = _mm256_shuffle_epi8 (a2YMM, ReverseMaskYMM); // Caution: the stupid intrinsic works on 128bit not on 256bit! b0YMM = b1+b0 not b0+b1
			b2YMM = _mm256_shuffle_epi8 (a0YMM, ReverseMaskYMM); 
// Should swap:
// https://godbolt.org/z/dY74zv1Ph
b0YMM = _mm256_permute4x64_epi64(b0YMM, 0b01001110); //# ymm0 = ymm0[2,3,0,1]
b2YMM = _mm256_permute4x64_epi64(b2YMM, 0b01001110); //# ymm0 = ymm0[2,3,0,1]

/*
__m256i _mm256_permute4x64_epi64 (__m256i a, const int imm8)
#include <immintrin.h>
Instruction: vpermq ymm, ymm, imm8
CPUID Flags: AVX2
Description
Shuffle 64-bit integers in a across lanes using the control in imm8, and store the results in dst.
Operation
DEFINE SELECT4(src, control) {
	CASE(control[1:0]) OF
	0:	tmp[63:0] := src[63:0]
	1:	tmp[63:0] := src[127:64]
	2:	tmp[63:0] := src[191:128]
	3:	tmp[63:0] := src[255:192]
	ESAC
	RETURN tmp[63:0]
}
dst[63:0] := SELECT4(a[255:0], imm8[1:0])
dst[127:64] := SELECT4(a[255:0], imm8[3:2])
dst[191:128] := SELECT4(a[255:0], imm8[5:4])
dst[255:192] := SELECT4(a[255:0], imm8[7:6])
dst[MAX:256] := 0
*/

//a8C7E8G7H5G3H1F2H3G1E2C1A2B4A6B8D7F8H7G5F7H8G6H4G2E1C2A1B3A5B7D8C6A7C8E7G8H6G4H2F1D2B1A3B5D6F5D4F3E5C4B2D3F4E6C5A4B6D5F6E4C3D1E3
//                             a0]                              a2]                              a0]                              a2]
//a8C7E8G7H5G3H1F2H3G1E2C1A2B4A6B8 D7F8H7G5F7H8G6H4G2E1C2A1B3A5B7D8 C6A7C8E7G8H6G4H2F1D2B1A3B5D6F5D4 F3E5C4B2D3F4E6C5A4B6D5F6E4C3D1E3

//a0: 61 38 43 37 | 45 38 47 37 | 48 35 47 33 | 48 31 46 32 ! 48 33 47 31 | 45 32 43 31 | 41 32 42 34 | 41 36 42 38
//a2: 44 37 46 38 | 48 37 47 35 | 46 37 48 38 | 47 36 48 34 ! 47 32 45 31 | 43 32 41 31 | 42 33 41 35 | 42 37 44 38
//b0: 34 48 36 47 | 38 48 37 46 | 35 47 37 48 | 38 46 37 44 ! 38 44 37 42 | 35 41 33 42 | 31 41 32 43 | 31 45 32 47
//b2: 32 46 31 48 | 33 47 35 48 | 37 47 38 45 | 37 43 38 61 ! 38 42 36 41 | 34 42 32 41 | 31 43 32 45 | 31 47 33 48

			//tmp0 = _mm_shuffle_epi8 (a0, PartialInterleavingMask1);
			//tmp1 = _mm_shuffle_epi8 (a0, PartialInterleavingMask2);
			//tmp2 = _mm_shuffle_epi8 (a2, PartialInterleavingMask3);
			//tmp3 = _mm_shuffle_epi8 (a2, PartialInterleavingMask4);
			//c0 = _mm_or_si128 (tmp0, tmp2);
			//c1 = _mm_or_si128 (tmp1, tmp3);
			//tmp0 = _mm_shuffle_epi8 (a1, PartialInterleavingMask1);
			//tmp1 = _mm_shuffle_epi8 (a1, PartialInterleavingMask2);
			//tmp2 = _mm_shuffle_epi8 (a3, PartialInterleavingMask3);
			//tmp3 = _mm_shuffle_epi8 (a3, PartialInterleavingMask4);
			//c2 = _mm_or_si128 (tmp0, tmp2);
			//c3 = _mm_or_si128 (tmp1, tmp3);
// c0: 00 20 01 21 | 02 22 03 23 | 04 24 05 25 | 06 26 07 27
// c1: 08 28 09 29 | 0a 2a 0b 2b | 0c 2c 0d 2d | 0e 2e 0f 2f
// c2: 10 30 11 31 | 12 32 13 33 | 14 34 15 35 | 16 36 17 37
// c3: 18 38 19 39 | 1a 3a 1b 3b | 1c 3c 1d 3d | 1e 3e 1f 3f
/*
__m256i _mm256_unpacklo_epi8 (__m256i a, __m256i b)
Synopsis
__m256i _mm256_unpacklo_epi8 (__m256i a, __m256i b)
#include <immintrin.h>
Instruction: vpunpcklbw ymm, ymm, ymm
CPUID Flags: AVX2
Description
Unpack and interleave 8-bit integers from the low half of each 128-bit lane in a and b, and store the results in dst.
Operation
DEFINE INTERLEAVE_BYTES(src1[127:0], src2[127:0]) {
	dst[7:0] := src1[7:0] 
	dst[15:8] := src2[7:0] 
	dst[23:16] := src1[15:8] 
	dst[31:24] := src2[15:8] 
	dst[39:32] := src1[23:16] 
	dst[47:40] := src2[23:16] 
	dst[55:48] := src1[31:24] 
	dst[63:56] := src2[31:24] 
	dst[71:64] := src1[39:32]
	dst[79:72] := src2[39:32] 
	dst[87:80] := src1[47:40] 
	dst[95:88] := src2[47:40] 
	dst[103:96] := src1[55:48] 
	dst[111:104] := src2[55:48] 
	dst[119:112] := src1[63:56] 
	dst[127:120] := src2[63:56] 
	RETURN dst[127:0]
}
dst[127:0] := INTERLEAVE_BYTES(a[127:0], b[127:0])
dst[255:128] := INTERLEAVE_BYTES(a[255:128], b[255:128])
dst[MAX:256] := 0
*/
//__m128i _mm_unpacklo_epi8 (__m128i a, __m128i b)

			c0YMM = _mm256_unpacklo_epi8 (a0YMM, a2YMM); 
			c2YMM = _mm256_unpackhi_epi8 (a0YMM, a2YMM); 
// Above two lines gave:
/*
    [                      0]   [                      1]   [                      2]   [                      3]
a0: 61 38 43 37 | 45 38 47 37 | 48 35 47 33 | 48 31 46 32 ! 48 33 47 31 | 45 32 43 31 | 41 32 42 34 | 41 36 42 38
    [                      4]   [                      5]   [                      6]   [                      7]
a2: 44 37 46 38 | 48 37 47 35 | 46 37 48 38 | 47 36 48 34 ! 47 32 45 31 | 43 32 41 31 | 42 33 41 35 | 42 37 44 38

    [                                                0+4]   [                                                2+6]   
c0: 61 44 38 37 | 43 46 37 38 | 45 48 38 37 | 47 47 37 35 ! 48 47 33 32 | 47 45 31 31 | 45 43 32 32 | 43 41 31 31
    [                                                1+5]   [                                                3+7]   
c2: 48 46 35 37 | 47 48 33 38 | 48 47 31 36 | 46 48 32 34 ! 41 42 32 33 | 42 41 34 35 | 41 42 36 37 | 42 44 38 38
*/
// But I need:
// c0,c1,c2,c3     not c0,c2,c1,c3
// 0+4,1+5,2+6,3+7 not 0+4,2+6,1+5,3+7
// as in XMM:
// c0: 00 20 01 21 | 02 22 03 23 | 04 24 05 25 | 06 26 07 27
// c1: 08 28 09 29 | 0a 2a 0b 2b | 0c 2c 0d 2d | 0e 2e 0f 2f
// c2: 10 30 11 31 | 12 32 13 33 | 14 34 15 35 | 16 36 17 37
// c3: 18 38 19 39 | 1a 3a 1b 3b | 1c 3c 1d 3d | 1e 3e 1f 3f

//a0: 61 38 43 37 | 45 38 47 37 | 48 35 47 33 | 48 31 46 32 ! 48 33 47 31 | 45 32 43 31 | 41 32 42 34 | 41 36 42 38
//a2: 44 37 46 38 | 48 37 47 35 | 46 37 48 38 | 47 36 48 34 ! 47 32 45 31 | 43 32 41 31 | 42 33 41 35 | 42 37 44 38

//tmp0YMM: 61 00 38 00 | 43 00 37 00 | 45 00 38 00 | 47 00 37 00 ! 41 00 32 00 | 42 00 34 00 | 41 00 36 00 | 42 00 38 00
//tmp2YMM: 00 44 00 37 | 00 46 00 38 | 00 48 00 37 | 00 47 00 35 ! 00 42 00 33 | 00 41 00 35 | 00 42 00 37 | 00 44 00 38

//			tmp0YMM = _mm256_shuffle_epi8 (a0YMM, PartialInterleavingMask1YMM); 
//			tmp2YMM = _mm256_shuffle_epi8 (a2YMM, PartialInterleavingMask3YMM); 
//			c0YMM = _mm256_or_si256 (tmp0YMM, tmp2YMM);
//			tmp0YMM = _mm256_shuffle_epi8 (a0YMM, PartialInterleavingMask1YMM); 
//			tmp2YMM = _mm256_shuffle_epi8 (a2YMM, PartialInterleavingMask3YMM); 
//			c2YMM = _mm256_or_si256 (tmp0YMM, tmp2YMM);

			//tmp0 = _mm_shuffle_epi8 (b0, PartialInterleavingMask1);
			//tmp1 = _mm_shuffle_epi8 (b0, PartialInterleavingMask2);
			//tmp2 = _mm_shuffle_epi8 (b2, PartialInterleavingMask3);
			//tmp3 = _mm_shuffle_epi8 (b2, PartialInterleavingMask4);
			//d0 = _mm_or_si128 (tmp0, tmp2);
			//d1 = _mm_or_si128 (tmp1, tmp3);
			//tmp0 = _mm_shuffle_epi8 (b1, PartialInterleavingMask1);
			//tmp1 = _mm_shuffle_epi8 (b1, PartialInterleavingMask2);
			//tmp2 = _mm_shuffle_epi8 (b3, PartialInterleavingMask3);
			//tmp3 = _mm_shuffle_epi8 (b3, PartialInterleavingMask4);
			//d2 = _mm_or_si128 (tmp0, tmp2);
			//d3 = _mm_or_si128 (tmp1, tmp3);
// d0: 3f 1f 3e 1e | 3d 1d 3c 1c | 3b 1b 3a 1a | 39 19 38 18
// d1: 37 17 36 16 | 35 15 34 14 | 33 13 32 12 | 31 11 30 10
// d2: 2f 0f 2e 0e | 2d 0d 2c 0c | 2b 0b 2a 0a | 29 09 28 08
// d3: 27 07 26 06 | 25 05 24 04 | 23 03 22 02 | 21 01 20 00
			// [[[ Next 6 lines are identical to simply REVERSE C vector - which is in 2 lines
			/*
			tmp0YMM = _mm256_shuffle_epi8 (b0YMM, PartialInterleavingMask1YMM); 
			tmp2YMM = _mm256_shuffle_epi8 (b2YMM, PartialInterleavingMask3YMM); 
			d0YMM = _mm256_or_si256 (tmp0YMM, tmp2YMM);
			tmp0YMM = _mm256_shuffle_epi8 (b0YMM, PartialInterleavingMask2YMM); 
			tmp2YMM = _mm256_shuffle_epi8 (b2YMM, PartialInterleavingMask4YMM); 
			d2YMM = _mm256_or_si256 (tmp0YMM, tmp2YMM);
			*/
			// ]]] Next 6 lines are identical to simply REVERSE C vector - which is in 2 lines, but I don't have it, so {{{
			d0YMM = _mm256_unpacklo_epi8 (b0YMM, b2YMM); 
			d2YMM = _mm256_unpackhi_epi8 (b0YMM, b2YMM); 
			// }}}

// For above code (the last thing to fix: b should be REVERSED a, not b1,b0,b3,b2):
//a0: 61 38 43 37 | 45 38 47 37 | 48 35 47 33 | 48 31 46 32 ! 48 33 47 31 | 45 32 43 31 | 41 32 42 34 | 41 36 42 38
//a2: 44 37 46 38 | 48 37 47 35 | 46 37 48 38 | 47 36 48 34 ! 47 32 45 31 | 43 32 41 31 | 42 33 41 35 | 42 37 44 38
//
//b0: 34 48 36 47 | 38 48 37 46 | 35 47 37 48 | 38 46 37 44 ! 38 44 37 42 | 35 41 33 42 | 31 41 32 43 | 31 45 32 47
//b2: 32 46 31 48 | 33 47 35 48 | 37 47 38 45 | 37 43 38 61 ! 38 42 36 41 | 34 42 32 41 | 31 43 32 45 | 31 47 33 48
//
//c0: 61 44 38 37 | 43 46 37 38 | 45 48 38 37 | 47 47 37 35 ! 48 47 33 32 | 47 45 31 31 | 45 43 32 32 | 43 41 31 31
//c2: 48 46 35 37 | 47 48 33 38 | 48 47 31 36 | 46 48 32 34 ! 41 42 32 33 | 42 41 34 35 | 41 42 36 37 | 42 44 38 38
//
//d0: 34 32 48 46 | 36 31 47 48 | 38 33 48 47 | 37 35 46 48 ! 38 38 44 42 | 37 36 42 41 | 35 34 41 42 | 33 32 42 41
//d2: 35 37 47 47 | 37 38 48 45 | 38 37 46 43 | 37 38 44 61 ! 31 31 41 43 | 32 32 43 45 | 31 31 45 47 | 32 33 47 48

/*
    _mm256_storeu_si256((__m256i*)vector, a0YMM);
    printf("a0: %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x ! %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x\n",
           vector[0], vector[1],  vector[2],  vector[3],  vector[4],  vector[5],  vector[6],  vector[7],
           vector[8], vector[9], vector[10], vector[11], vector[12], vector[13], vector[14], vector[15],
           vector[0+16], vector[1+16],  vector[2+16],  vector[3+16],  vector[4+16],  vector[5+16],  vector[6+16],  vector[7+16],
           vector[8+16], vector[9+16], vector[10+16], vector[11+16], vector[12+16], vector[13+16], vector[14+16], vector[15+16]);

    _mm256_storeu_si256((__m256i*)vector, a2YMM);
    printf("a2: %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x ! %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x\n",
           vector[0], vector[1],  vector[2],  vector[3],  vector[4],  vector[5],  vector[6],  vector[7],
           vector[8], vector[9], vector[10], vector[11], vector[12], vector[13], vector[14], vector[15],
           vector[0+16], vector[1+16],  vector[2+16],  vector[3+16],  vector[4+16],  vector[5+16],  vector[6+16],  vector[7+16],
           vector[8+16], vector[9+16], vector[10+16], vector[11+16], vector[12+16], vector[13+16], vector[14+16], vector[15+16]);

    _mm256_storeu_si256((__m256i*)vector, b0YMM);
    printf("b0: %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x ! %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x\n",
           vector[0], vector[1],  vector[2],  vector[3],  vector[4],  vector[5],  vector[6],  vector[7],
           vector[8], vector[9], vector[10], vector[11], vector[12], vector[13], vector[14], vector[15],
           vector[0+16], vector[1+16],  vector[2+16],  vector[3+16],  vector[4+16],  vector[5+16],  vector[6+16],  vector[7+16],
           vector[8+16], vector[9+16], vector[10+16], vector[11+16], vector[12+16], vector[13+16], vector[14+16], vector[15+16]);

    _mm256_storeu_si256((__m256i*)vector, b2YMM);
    printf("b2: %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x ! %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x\n",
           vector[0], vector[1],  vector[2],  vector[3],  vector[4],  vector[5],  vector[6],  vector[7],
           vector[8], vector[9], vector[10], vector[11], vector[12], vector[13], vector[14], vector[15],
           vector[0+16], vector[1+16],  vector[2+16],  vector[3+16],  vector[4+16],  vector[5+16],  vector[6+16],  vector[7+16],
           vector[8+16], vector[9+16], vector[10+16], vector[11+16], vector[12+16], vector[13+16], vector[14+16], vector[15+16]);

    _mm256_storeu_si256((__m256i*)vector, c0YMM);
    printf("c0: %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x ! %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x\n",
           vector[0], vector[1],  vector[2],  vector[3],  vector[4],  vector[5],  vector[6],  vector[7],
           vector[8], vector[9], vector[10], vector[11], vector[12], vector[13], vector[14], vector[15],
           vector[0+16], vector[1+16],  vector[2+16],  vector[3+16],  vector[4+16],  vector[5+16],  vector[6+16],  vector[7+16],
           vector[8+16], vector[9+16], vector[10+16], vector[11+16], vector[12+16], vector[13+16], vector[14+16], vector[15+16]);
    _mm256_storeu_si256((__m256i*)vector, c2YMM);
    printf("c2: %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x ! %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x\n",
           vector[0], vector[1],  vector[2],  vector[3],  vector[4],  vector[5],  vector[6],  vector[7],
           vector[8], vector[9], vector[10], vector[11], vector[12], vector[13], vector[14], vector[15],
           vector[0+16], vector[1+16],  vector[2+16],  vector[3+16],  vector[4+16],  vector[5+16],  vector[6+16],  vector[7+16],
           vector[8+16], vector[9+16], vector[10+16], vector[11+16], vector[12+16], vector[13+16], vector[14+16], vector[15+16]);

    _mm256_storeu_si256((__m256i*)vector, d0YMM);
    printf("d0: %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x ! %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x\n",
           vector[0], vector[1],  vector[2],  vector[3],  vector[4],  vector[5],  vector[6],  vector[7],
           vector[8], vector[9], vector[10], vector[11], vector[12], vector[13], vector[14], vector[15],
           vector[0+16], vector[1+16],  vector[2+16],  vector[3+16],  vector[4+16],  vector[5+16],  vector[6+16],  vector[7+16],
           vector[8+16], vector[9+16], vector[10+16], vector[11+16], vector[12+16], vector[13+16], vector[14+16], vector[15+16]);
    _mm256_storeu_si256((__m256i*)vector, d2YMM);
    printf("d2: %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x ! %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x\n",
           vector[0], vector[1],  vector[2],  vector[3],  vector[4],  vector[5],  vector[6],  vector[7],
           vector[8], vector[9], vector[10], vector[11], vector[12], vector[13], vector[14], vector[15],
           vector[0+16], vector[1+16],  vector[2+16],  vector[3+16],  vector[4+16],  vector[5+16],  vector[6+16],  vector[7+16],
           vector[8+16], vector[9+16], vector[10+16], vector[11+16], vector[12+16], vector[13+16], vector[14+16], vector[15+16]);

    printf("\n");
*/

/*
G:\Lookupperorama_r13\COLLISION_Hashliner>Hashliner_DDAES_dump5byteshash.exe 1.KnightTours.txt
394e907d43

G:\Lookupperorama_r13\COLLISION_Hashliner>Hashliner_DDAES-XMM_dump5byteshash.exe 1.KnightTours.txt
f4b027e3ab

G:\Lookupperorama_r13\COLLISION_Hashliner>d:

D:\>g:

G:\Lookupperorama_r13\COLLISION_Hashliner>Hashliner_DDAES_dump5byteshash.exe 1.KnightTours.txt
a0: 61 38 43 37 | 45 38 47 37 | 48 35 47 33 | 48 31 46 32 ! 48 33 47 31 | 45 32 43 31 | 41 32 42 34 | 41 36 42 38
a2: 44 37 46 38 | 48 37 47 35 | 46 37 48 38 | 47 36 48 34 ! 47 32 45 31 | 43 32 41 31 | 42 33 41 35 | 42 37 44 38
c0: 61 44 38 37 | 43 46 37 38 | 45 48 38 37 | 47 47 37 35 ! 41 42 32 33 | 42 41 34 35 | 41 42 36 37 | 42 44 38 38
c2: 61 44 38 37 | 43 46 37 38 | 45 48 38 37 | 47 47 37 35 ! 41 42 32 33 | 42 41 34 35 | 41 42 36 37 | 42 44 38 38

a0: 43 36 41 37 | 43 38 45 37 | 47 38 48 36 | 47 34 48 32 ! 46 31 44 32 | 42 31 41 33 | 42 35 44 36 | 46 35 44 34
a2: 46 33 45 35 | 43 34 42 32 | 44 33 46 34 | 45 36 43 35 ! 41 34 42 36 | 44 35 46 36 | 45 34 43 33 | 44 31 45 33
c0: 43 46 36 33 | 41 45 37 35 | 43 43 38 34 | 45 42 37 32 ! 42 45 35 34 | 44 43 36 33 | 46 44 35 31 | 44 45 34 33
c2: 43 46 36 33 | 41 45 37 35 | 43 43 38 34 | 45 42 37 32 ! 42 45 35 34 | 44 43 36 33 | 46 44 35 31 | 44 45 34 33

394e907d43

G:\Lookupperorama_r13\COLLISION_Hashliner>type 1.KnightTours.txt
a8C7E8G7H5G3H1F2H3G1E2C1A2B4A6B8D7F8H7G5F7H8G6H4G2E1C2A1B3A5B7D8C6A7C8E7G8H6G4H2F1D2B1A3B5D6F5D4F3E5C4B2D3F4E6C5A4B6D5F6E4C3D1E3

G:\Lookupperorama_r13\COLLISION_Hashliner>
                             a0]                              a2]                              a0]                              a2]
a8C7E8G7H5G3H1F2H3G1E2C1A2B4A6B8 D7F8H7G5F7H8G6H4G2E1C2A1B3A5B7D8 C6A7C8E7G8H6G4H2F1D2B1A3B5D6F5D4 F3E5C4B2D3F4E6C5A4B6D5F6E4C3D1E3

*/

/*
    _mm_storeu_si128((__m128i*)vector, *(__m128i *)(&a0YMM));
    printf("a0lo: %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x\n",
           vector[0], vector[1],  vector[2],  vector[3],  vector[4],  vector[5],  vector[6],  vector[7],
           vector[8], vector[9], vector[10], vector[11], vector[12], vector[13], vector[14], vector[15]);
    _mm_storeu_si128((__m128i*)vector, *((__m128i *)(&a0YMM)+1));
    printf("a0hi: %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x | %02x %02x %02x %02x\n",
           vector[0], vector[1],  vector[2],  vector[3],  vector[4],  vector[5],  vector[6],  vector[7],
           vector[8], vector[9], vector[10], vector[11], vector[12], vector[13], vector[14], vector[15]);

    printf("Above two should equal a0YMM\n");
*/
//void SlowCopy128bit (const char *SOURCE, char *TARGET) { _mm_storeu_si128((__m128i *)(TARGET), _mm_loadu_si128((const __m128i *)(SOURCE))); }

// Okay, the final dump (shows XMM and YMM variants produce the same hash):
/*

D:\Lookupperorama_r13\COLLISION_Hashliner>Hashliner_DDAES_dump5byteshash.exe 1.KnightTours.txt
a0: 61 38 43 37 | 45 38 47 37 | 48 35 47 33 | 48 31 46 32
a1: 48 33 47 31 | 45 32 43 31 | 41 32 42 34 | 41 36 42 38
a2: 44 37 46 38 | 48 37 47 35 | 46 37 48 38 | 47 36 48 34
a3: 47 32 45 31 | 43 32 41 31 | 42 33 41 35 | 42 37 44 38

b0: 38 44 37 42 | 35 41 33 42 | 31 41 32 43 | 31 45 32 47
b1: 34 48 36 47 | 38 48 37 46 | 35 47 37 48 | 38 46 37 44
b2: 38 42 36 41 | 34 42 32 41 | 31 43 32 45 | 31 47 33 48
b3: 32 46 31 48 | 33 47 35 48 | 37 47 38 45 | 37 43 38 61

c0: 61 44 38 37 | 43 46 37 38 | 45 48 38 37 | 47 47 37 35
c1: 48 46 35 37 | 47 48 33 38 | 48 47 31 36 | 46 48 32 34
c2: 48 47 33 32 | 47 45 31 31 | 45 43 32 32 | 43 41 31 31
c3: 41 42 32 33 | 42 41 34 35 | 41 42 36 37 | 42 44 38 38

d0: 38 38 44 42 | 37 36 42 41 | 35 34 41 42 | 33 32 42 41
d1: 31 31 41 43 | 32 32 43 45 | 31 31 45 47 | 32 33 47 48
d2: 34 32 48 46 | 36 31 47 48 | 38 33 48 47 | 37 35 46 48
d3: 35 37 47 47 | 37 38 48 45 | 38 37 46 43 | 37 38 44 61

a0: 43 36 41 37 | 43 38 45 37 | 47 38 48 36 | 47 34 48 32
a1: 46 31 44 32 | 42 31 41 33 | 42 35 44 36 | 46 35 44 34
a2: 46 33 45 35 | 43 34 42 32 | 44 33 46 34 | 45 36 43 35
a3: 41 34 42 36 | 44 35 46 36 | 45 34 43 33 | 44 31 45 33

b0: 33 45 31 44 | 33 43 34 45 | 36 46 35 44 | 36 42 34 41
b1: 35 43 36 45 | 34 46 33 44 | 32 42 34 43 | 35 45 33 46
b2: 34 44 35 46 | 36 44 35 42 | 33 41 31 42 | 32 44 31 46
b3: 32 48 34 47 | 36 48 38 47 | 37 45 38 43 | 37 41 36 43

c0: 43 46 36 33 | 41 45 37 35 | 43 43 38 34 | 45 42 37 32
c1: 47 44 38 33 | 48 46 36 34 | 47 45 34 36 | 48 43 32 35
c2: 46 41 31 34 | 44 42 32 36 | 42 44 31 35 | 41 46 33 36
c3: 42 45 35 34 | 44 43 36 33 | 46 44 35 31 | 44 45 34 33

d0: 33 34 45 44 | 31 35 44 46 | 33 36 43 44 | 34 35 45 42
d1: 36 33 46 41 | 35 31 44 42 | 36 32 42 44 | 34 31 41 46
d2: 35 32 43 48 | 36 34 45 47 | 34 36 46 48 | 33 38 44 47
d3: 32 37 42 45 | 34 38 43 43 | 35 37 45 41 | 33 36 46 43

f4b027e3ab
a0: 61 38 43 37 | 45 38 47 37 | 48 35 47 33 | 48 31 46 32 ! 48 33 47 31 | 45 32 43 31 | 41 32 42 34 | 41 36 42 38
a2: 44 37 46 38 | 48 37 47 35 | 46 37 48 38 | 47 36 48 34 ! 47 32 45 31 | 43 32 41 31 | 42 33 41 35 | 42 37 44 38
b0: 38 44 37 42 | 35 41 33 42 | 31 41 32 43 | 31 45 32 47 ! 34 48 36 47 | 38 48 37 46 | 35 47 37 48 | 38 46 37 44
b2: 38 42 36 41 | 34 42 32 41 | 31 43 32 45 | 31 47 33 48 ! 32 46 31 48 | 33 47 35 48 | 37 47 38 45 | 37 43 38 61
c0: 61 44 38 37 | 43 46 37 38 | 45 48 38 37 | 47 47 37 35 ! 48 47 33 32 | 47 45 31 31 | 45 43 32 32 | 43 41 31 31
c2: 48 46 35 37 | 47 48 33 38 | 48 47 31 36 | 46 48 32 34 ! 41 42 32 33 | 42 41 34 35 | 41 42 36 37 | 42 44 38 38
d0: 38 38 44 42 | 37 36 42 41 | 35 34 41 42 | 33 32 42 41 ! 34 32 48 46 | 36 31 47 48 | 38 33 48 47 | 37 35 46 48
d2: 31 31 41 43 | 32 32 43 45 | 31 31 45 47 | 32 33 47 48 ! 35 37 47 47 | 37 38 48 45 | 38 37 46 43 | 37 38 44 61

a0lo: 61 38 43 37 | 45 38 47 37 | 48 35 47 33 | 48 31 46 32
a0hi: 48 33 47 31 | 45 32 43 31 | 41 32 42 34 | 41 36 42 38
Above two should equal a0YMM
a0: 43 36 41 37 | 43 38 45 37 | 47 38 48 36 | 47 34 48 32 ! 46 31 44 32 | 42 31 41 33 | 42 35 44 36 | 46 35 44 34
a2: 46 33 45 35 | 43 34 42 32 | 44 33 46 34 | 45 36 43 35 ! 41 34 42 36 | 44 35 46 36 | 45 34 43 33 | 44 31 45 33
b0: 33 45 31 44 | 33 43 34 45 | 36 46 35 44 | 36 42 34 41 ! 35 43 36 45 | 34 46 33 44 | 32 42 34 43 | 35 45 33 46
b2: 34 44 35 46 | 36 44 35 42 | 33 41 31 42 | 32 44 31 46 ! 32 48 34 47 | 36 48 38 47 | 37 45 38 43 | 37 41 36 43
c0: 43 46 36 33 | 41 45 37 35 | 43 43 38 34 | 45 42 37 32 ! 46 41 31 34 | 44 42 32 36 | 42 44 31 35 | 41 46 33 36
c2: 47 44 38 33 | 48 46 36 34 | 47 45 34 36 | 48 43 32 35 ! 42 45 35 34 | 44 43 36 33 | 46 44 35 31 | 44 45 34 33
d0: 33 34 45 44 | 31 35 44 46 | 33 36 43 44 | 34 35 45 42 ! 35 32 43 48 | 36 34 45 47 | 34 36 46 48 | 33 38 44 47
d2: 36 33 46 41 | 35 31 44 42 | 36 32 42 44 | 34 31 41 46 ! 32 37 42 45 | 34 38 43 43 | 35 37 45 41 | 33 36 46 43

a0lo: 43 36 41 37 | 43 38 45 37 | 47 38 48 36 | 47 34 48 32
a0hi: 46 31 44 32 | 42 31 41 33 | 42 35 44 36 | 46 35 44 34
Above two should equal a0YMM
f4b027e3ab

D:\Lookupperorama_r13\COLLISION_Hashliner>
*/

			//In YMM should swap c1 and c2, also d1 and d2

			//hashA = _mm_aesenc_si128(hashA, a0);
			hashA = _mm_aesenc_si128(hashA, *(__m128i *)(&a0YMM));
			//hashB = _mm_aesenc_si128(hashB, b0);
				hashB = _mm_aesenc_si128(hashB, *(__m128i *)(&b0YMM));
				//hashB = _mm_aesenc_si128(hashB, *((__m128i *)(&b0YMM)+1));
			//hashC = _mm_aesenc_si128(hashC, c0);
			hashC = _mm_aesenc_si128(hashC, *(__m128i *)(&c0YMM));
			//hashD = _mm_aesenc_si128(hashD, d0);
			hashD = _mm_aesenc_si128(hashD, *(__m128i *)(&d0YMM));

			//hashA = _mm_aesenc_si128(hashA, a1);
			hashA = _mm_aesenc_si128(hashA, *((__m128i *)(&a0YMM)+1));
			//hashB = _mm_aesenc_si128(hashB, b1);
				hashB = _mm_aesenc_si128(hashB, *((__m128i *)(&b0YMM)+1));
				//hashB = _mm_aesenc_si128(hashB, *(__m128i *)(&b0YMM));
			//hashC = _mm_aesenc_si128(hashC, c1);
			hashC = _mm_aesenc_si128(hashC, *((__m128i *)(&c2YMM)));
			//hashD = _mm_aesenc_si128(hashD, d1);
			hashD = _mm_aesenc_si128(hashD, *((__m128i *)(&d2YMM)));

			//hashA = _mm_aesenc_si128(hashA, a2);
			hashA = _mm_aesenc_si128(hashA, *(__m128i *)(&a2YMM));
			//hashB = _mm_aesenc_si128(hashB, b2);
				hashB = _mm_aesenc_si128(hashB, *(__m128i *)(&b2YMM));
				//hashB = _mm_aesenc_si128(hashB, *((__m128i *)(&b2YMM)+1));
			//hashC = _mm_aesenc_si128(hashC, c2);
			hashC = _mm_aesenc_si128(hashC, *((__m128i *)(&c0YMM)+1));
			//hashD = _mm_aesenc_si128(hashD, d2);
			hashD = _mm_aesenc_si128(hashD, *((__m128i *)(&d0YMM)+1));

			//hashA = _mm_aesenc_si128(hashA, a3);
			hashA = _mm_aesenc_si128(hashA, *((__m128i *)(&a2YMM)+1));
			//hashB = _mm_aesenc_si128(hashB, b3);
				hashB = _mm_aesenc_si128(hashB, *((__m128i *)(&b2YMM)+1));
				//hashB = _mm_aesenc_si128(hashB, *(__m128i *)(&b2YMM));
			//hashC = _mm_aesenc_si128(hashC, c3);
			hashC = _mm_aesenc_si128(hashC, *((__m128i *)(&c2YMM)+1));
			//hashD = _mm_aesenc_si128(hashD, d3);
			hashD = _mm_aesenc_si128(hashD, *((__m128i *)(&d2YMM)+1));

			hashA = _mm_aesenc_si128(hashA, hashB);
			hashA = _mm_aesenc_si128(hashA, hashC);
			hashA = _mm_aesenc_si128(hashA, hashD);
			length = length - 64;
		}
	}

	ptr128 = (__m128i *)buffer;
	if (length >=16) {
		Cycles = length/16;
		for(; Cycles--; buffer += 16) {
			AgainstRules = _mm_loadu_si128(ptr128++);
			GumbotronREVER = _mm_shuffle_epi8 (AgainstRules, ReverseMask);
			GumbotronINTER = _mm_shuffle_epi8 (AgainstRules, InterleaveMask);
			GumbotronREVERINTER = _mm_shuffle_epi8 (GumbotronREVER, InterleaveMask);
			hashA = _mm_aesenc_si128(hashA, AgainstRules);
			hashB = _mm_aesenc_si128(hashB, GumbotronREVER);
			hashC = _mm_aesenc_si128(hashC, GumbotronINTER);
			hashD = _mm_aesenc_si128(hashD, GumbotronREVERINTER);
			hashA = _mm_aesenc_si128(hashA, hashB);
			hashA = _mm_aesenc_si128(hashA, hashC);
			hashA = _mm_aesenc_si128(hashA, hashD);
			length = length - 16;
		}
	} 
	// Inhere using Pippip's approach to read past the end ("the dirty" sentinel like style, or more like padding):
	if (length&(16-1)) {
		AgainstRules = _mm_loadu_si128(ptr128);		
		//AgainstRules = _mm_srli_si128 (AgainstRules, 16-length); // catastrophic error: Intrinsic parameter must be an immediate value
		AgainstRules = _mm_and_si128 (AgainstRules, Mumbotron[length]);
		//Gumbotron = _mm_slli_si128 (Gumbotron, 16-length); // catastrophic error: Intrinsic parameter must be an immediate value
		Gumbotron = _mm_and_si128 (hashB, Jumbotron[length]);
		AgainstRules = _mm_or_si128 (AgainstRules, Gumbotron);

		GumbotronREVER = _mm_shuffle_epi8 (AgainstRules, ReverseMask);
		GumbotronINTER = _mm_shuffle_epi8 (AgainstRules, InterleaveMask);
		GumbotronREVERINTER = _mm_shuffle_epi8 (GumbotronREVER, InterleaveMask);
			hashA = _mm_aesenc_si128(hashA, AgainstRules);
			hashB = _mm_aesenc_si128(hashB, GumbotronREVER);
			hashC = _mm_aesenc_si128(hashC, GumbotronINTER);
			hashD = _mm_aesenc_si128(hashD, GumbotronREVERINTER);
			hashA = _mm_aesenc_si128(hashA, hashB);
			hashA = _mm_aesenc_si128(hashA, hashC);
			hashA = _mm_aesenc_si128(hashA, hashD);
	}
	SlowCopy128bit( (const char *)(&hashA), (char *)&DDAES[0]);
}
```
    
The following code is the ASM generated by https://godbolt.org/ x86-64 clang 20.1.0 with compile options: -O3 -maes -mavx2
    
```
SlowCopy128bit:
  vmovups xmm0, xmmword ptr [rdi]
  vmovups xmmword ptr [rsi], xmm0
  ret

.LCPI1_0:
  .quad 3285590903173248716
  .quad -2511163900754046922
.LCPI1_1:
  .quad 1162971903282775349
  .quad -3985312487901316173
.LCPI1_2:
  .quad 7113472399480571277
  .quad 7809847782465536322
.LCPI1_4:
  .byte 15
  .byte 14
  .byte 13
  .byte 12
  .byte 11
  .byte 10
  .byte 9
  .byte 8
  .byte 7
  .byte 6
  .byte 5
  .byte 4
  .byte 3
  .byte 2
  .byte 1
  .byte 0
.LCPI1_5:
  .byte 0
  .byte 8
  .byte 1
  .byte 9
  .byte 2
  .byte 10
  .byte 3
  .byte 11
  .byte 4
  .byte 12
  .byte 5
  .byte 13
  .byte 6
  .byte 14
  .byte 7
  .byte 15
.LCPI1_6:
  .byte 15
  .byte 7
  .byte 14
  .byte 6
  .byte 13
  .byte 5
  .byte 12
  .byte 4
  .byte 11
  .byte 3
  .byte 10
  .byte 2
  .byte 9
  .byte 1
  .byte 8
  .byte 0
DoubleDeuceAES_Gumbotron_YMM:
  cmp rsi, 64
  jae .LBB1_2
  vmovdqa xmm0, xmmword ptr [rip + .LCPI1_0]
  vmovdqa xmm2, xmmword ptr [rip + .LCPI1_1]
  vpxor xmm1, xmm1, xmm1
  vmovdqa xmm3, xmmword ptr [rip + .LCPI1_2]
  cmp rsi, 16
  jae .LBB1_6
  jmp .LBB1_10
.LBB1_2:
  mov rcx, rsi
  shr rcx, 6
  mov rax, rsi
  and rax, -64
  and esi, 63
  vmovdqa xmm3, xmmword ptr [rip + .LCPI1_2]
  vpxor xmm1, xmm1, xmm1
  vmovdqa xmm2, xmmword ptr [rip + .LCPI1_1]
  vmovdqa xmm0, xmmword ptr [rip + .LCPI1_0]
  vbroadcasti128 ymm4, xmmword ptr [rip + .LCPI1_4]
  mov rdx, rdi
.LBB1_3:
  vmovdqu ymm6, ymmword ptr [rdx]
  vmovdqu ymm7, ymmword ptr [rdx + 32]
  vpshufb ymm8, ymm7, ymm4
  vpshufb ymm5, ymm6, ymm4
  vpermq ymm9, ymm8, 78
  vpermq ymm10, ymm5, 78
  vpunpcklbw ymm11, ymm6, ymm7
  vpunpckhbw ymm12, ymm6, ymm7
  vaesenc xmm3, xmm3, xmm6
  vpunpcklbw ymm6, ymm9, ymm10
  vextracti128 xmm13, ymm8, 1
  vaesenc xmm0, xmm0, xmm13
  vaesenc xmm2, xmm2, xmm11
  vaesenc xmm1, xmm1, xmm6
  vpunpckhbw ymm9, ymm9, ymm10
  vaesenc xmm3, xmm3, xmmword ptr [rdx + 16]
  vaesenc xmm0, xmm0, xmm8
  vaesenc xmm2, xmm2, xmm12
  vaesenc xmm1, xmm1, xmm9
  vaesenc xmm3, xmm3, xmm7
  vextracti128 xmm7, ymm5, 1
  vaesenc xmm0, xmm0, xmm7
  vextracti128 xmm7, ymm11, 1
  vaesenc xmm2, xmm2, xmm7
  vextracti128 xmm6, ymm6, 1
  vaesenc xmm3, xmm3, xmmword ptr [rdx + 48]
  vaesenc xmm1, xmm1, xmm6
  vaesenc xmm0, xmm0, xmm5
  vextracti128 xmm5, ymm12, 1
  vaesenc xmm2, xmm2, xmm5
  add rdx, 64
  vextracti128 xmm5, ymm9, 1
  vaesenc xmm1, xmm1, xmm5
  vaesenc xmm3, xmm3, xmm0
  vaesenc xmm3, xmm3, xmm2
  vaesenc xmm3, xmm3, xmm1
  dec rcx
  jne .LBB1_3
  add rdi, rax
  cmp rsi, 16
  jb .LBB1_10
.LBB1_6:
  mov rcx, rsi
  shr rcx, 4
  mov eax, esi
  vmovdqu xmm4, xmmword ptr [rdi]
  vpshufb xmm5, xmm4, xmmword ptr [rip + .LCPI1_4]
  vpshufb xmm6, xmm4, xmmword ptr [rip + .LCPI1_5]
  vpshufb xmm7, xmm4, xmmword ptr [rip + .LCPI1_6]
  vaesenc xmm3, xmm3, xmm4
  vaesenc xmm0, xmm0, xmm5
  vaesenc xmm2, xmm2, xmm6
  vaesenc xmm1, xmm1, xmm7
  vaesenc xmm3, xmm3, xmm0
  vaesenc xmm3, xmm3, xmm2
  and eax, 48
  vaesenc xmm3, xmm3, xmm1
  cmp rcx, 1
  je .LBB1_9
  vmovdqu xmm4, xmmword ptr [rdi + 16]
  vpshufb xmm5, xmm4, xmmword ptr [rip + .LCPI1_4]
  vpshufb xmm6, xmm4, xmmword ptr [rip + .LCPI1_5]
  vpshufb xmm7, xmm4, xmmword ptr [rip + .LCPI1_6]
  vaesenc xmm3, xmm3, xmm4
  vaesenc xmm0, xmm0, xmm5
  vaesenc xmm2, xmm2, xmm6
  vaesenc xmm1, xmm1, xmm7
  vaesenc xmm3, xmm3, xmm0
  vaesenc xmm3, xmm3, xmm2
  vaesenc xmm3, xmm3, xmm1
  cmp rcx, 2
  je .LBB1_9
  vmovdqu xmm4, xmmword ptr [rdi + 32]
  vpshufb xmm5, xmm4, xmmword ptr [rip + .LCPI1_4]
  vpshufb xmm6, xmm4, xmmword ptr [rip + .LCPI1_5]
  vpshufb xmm7, xmm4, xmmword ptr [rip + .LCPI1_6]
  vaesenc xmm3, xmm3, xmm4
  vaesenc xmm0, xmm0, xmm5
  vaesenc xmm2, xmm2, xmm6
  vaesenc xmm1, xmm1, xmm7
  vaesenc xmm3, xmm3, xmm0
  vaesenc xmm3, xmm3, xmm2
  vaesenc xmm3, xmm3, xmm1
.LBB1_9:
  add rdi, rax
  and esi, 15
.LBB1_10:
  test rsi, rsi
  je .LBB1_12
  shl rsi, 4
  lea rax, [rip + VectorsNeedNonVAriable1]
  vmovdqa xmm4, xmmword ptr [rsi + rax]
  vpand xmm4, xmm4, xmmword ptr [rdi]
  lea rax, [rip + VectorsNeedNonVAriable2]
  vpand xmm5, xmm0, xmmword ptr [rsi + rax]
  vpor xmm4, xmm5, xmm4
  vpshufb xmm5, xmm4, xmmword ptr [rip + .LCPI1_4]
  vpshufb xmm6, xmm4, xmmword ptr [rip + .LCPI1_5]
  vaesenc xmm2, xmm2, xmm6
  vpshufb xmm6, xmm4, xmmword ptr [rip + .LCPI1_6]
  vaesenc xmm1, xmm1, xmm6
  vaesenc xmm3, xmm3, xmm4
  vaesenc xmm0, xmm0, xmm5
  vaesenc xmm0, xmm3, xmm0
  vaesenc xmm0, xmm0, xmm2
  vaesenc xmm3, xmm0, xmm1
.LBB1_12:
  vmovdqa xmmword ptr [rip + DDAES], xmm3
  vzeroupper
  ret

DDAES:
  .zero 16

VectorsNeedNonVAriable1:
  .asciz "\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\377\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\377\377\000\000\000\000\000\000\000\000\000\000\000\000\000\000\377\377\377\000\000\000\000\000\000\000\000\000\000\000\000\000\377\377\377\377\000\000\000\000\000\000\000\000\000\000\000\000\377\377\377\377\377\000\000\000\000\000\000\000\000\000\000\000\377\377\377\377\377\377\000\000\000\000\000\000\000\000\000\000\377\377\377\377\377\377\377\000\000\000\000\000\000\000\000\000\377\377\377\377\377\377\377\377\000\000\000\000\000\000\000\000\377\377\377\377\377\377\377\377\377\000\000\000\000\000\000\000\377\377\377\377\377\377\377\377\377\377\000\000\000\000\000\000\377\377\377\377\377\377\377\377\377\377\377\000\000\000\000\000\377\377\377\377\377\377\377\377\377\377\377\377\000\000\000\000\377\377\377\377\377\377\377\377\377\377\377\377\377\000\000\000\377\377\377\377\377\377\377\377\377\377\377\377\377\377\000\000\377\377\377\377\377\377\377\377\377\377\377\377\377\377\377"

VectorsNeedNonVAriable2:
  .ascii "\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\377\377\377\377\377\377\377\377\377\377\377\377\377\377\377\000\000\377\377\377\377\377\377\377\377\377\377\377\377\377\377\000\000\000\377\377\377\377\377\377\377\377\377\377\377\377\377\000\000\000\000\377\377\377\377\377\377\377\377\377\377\377\377\000\000\000\000\000\377\377\377\377\377\377\377\377\377\377\377\000\000\000\000\000\000\377\377\377\377\377\377\377\377\377\377\000\000\000\000\000\000\000\377\377\377\377\377\377\377\377\377\000\000\000\000\000\000\000\000\377\377\377\377\377\377\377\377\000\000\000\000\000\000\000\000\000\377\377\377\377\377\377\377\000\000\000\000\000\000\000\000\000\000\377\377\377\377\377\377\000\000\000\000\000\000\000\000\000\000\000\377\377\377\377\377\000\000\000\000\000\000\000\000\000\000\000\000\377\377\377\377\000\000\000\000\000\000\000\000\000\000\000\000\000\377\377\377\000\000\000\000\000\000\000\000\000\000\000\000\000\000\377\377\000\000\000\000\000\000\000\000\000\000\000\000\000\000\000\377"
```
    
Let us see who is the fastest 128bit hasher (for 128 bytes long keys, inhere) ...
    
The package (all sources included) allowing to reproduce all the runs is freely downloadable at:  
www.sanmayce.com/Gumbotron_vs_XXH128.zip  
https://github.com/Sanmayce/DoubleDeuce/releases/download/r1/Gumbotron_vs_XXH128.zip
    
The 200M keys were chosen in order to fit into 32GB machines, something like 25GB needed.
    
On laptop 'Compressionette' (Kaby Lake i5-7200U 2.5GHz (3.1GHz max turbo) 36GB DDR4 2133MHz, Windows 10) Gumbotron_YMM hashes 4,487/3,160= 1.41x faster than XXH128:
```
G:\Lookupperorama_r13\COLLISION_Hashliner>GvsXXH.bat

G:\Lookupperorama_r13\COLLISION_Hashliner>if exist 200000000.KnightTours.txt goto Skip

G:\Lookupperorama_r13\COLLISION_Hashliner>BenchHashingLines_Gumbotron.exe 200000000.KnightTours.txt
Hashing 200,000,000 lines/keys, 128 bytes each, in RAM ...
The first key has hash:
f4b027e3ab
Total time: 4,907 clocks.
Total time: 3,186 clocks.
Total time: 3,162 clocks.
Total time: 3,160 clocks.
Total time: 3,185 clocks.
Total time: 3,174 clocks.
Total time: 3,160 clocks.
Total time: 3,164 clocks.
Total time: 3,167 clocks.
Total time: 3,174 clocks.
Total time: 3,183 clocks.
Total time: 3,174 clocks.
Total time: 3,165 clocks.
Total time: 3,172 clocks.
Total time: 3,161 clocks.
Total time: 3,170 clocks.
Total time: 3,165 clocks.
Total time (BEST RUN): 3,160 clocks.

G:\Lookupperorama_r13\COLLISION_Hashliner>BenchHashingLines_XXH128.exe 200000000.KnightTours.txt
Hashing 200,000,000 lines/keys, 128 bytes each, in RAM ...
The first key has hash:
c703b0bd77
Total time: 6,021 clocks.
Total time: 4,492 clocks.
Total time: 4,532 clocks.
Total time: 4,509 clocks.
Total time: 4,492 clocks.
Total time: 4,490 clocks.
Total time: 4,495 clocks.
Total time: 4,494 clocks.
Total time: 4,492 clocks.
Total time: 4,491 clocks.
Total time: 4,496 clocks.
Total time: 4,487 clocks.
Total time: 4,491 clocks.
Total time: 4,493 clocks.
Total time: 4,496 clocks.
Total time: 4,494 clocks.
Total time: 4,512 clocks.
Total time (BEST RUN): 4,487 clocks.

G:\Lookupperorama_r13\COLLISION_Hashliner>
```
    
On laptop 'Brutalitto' (Renoir AMD 4800H max turbo 4.3GHz, 64GB DDR4 3200MHz, Windows 10) Gumbotron_YMM hashes 3,266/2,484= 1.31x faster than XXH128:
```
D:\Lookupperorama_r13\COLLISION_Hashliner>GvsXXH.bat

D:\Lookupperorama_r13\COLLISION_Hashliner>if exist 200000000.KnightTours.txt goto Skip

D:\Lookupperorama_r13\COLLISION_Hashliner>BenchHashingLines_Gumbotron.exe 200000000.KnightTours.txt
Hashing 200,000,000 lines/keys, 128 bytes each, in RAM ...
The first key has hash:
f4b027e3ab
Total time: 3,313 clocks.
Total time: 2,485 clocks.
Total time: 2,500 clocks.
Total time: 2,484 clocks.
Total time: 2,501 clocks.
Total time: 2,500 clocks.
Total time: 2,500 clocks.
Total time: 2,500 clocks.
Total time: 2,500 clocks.
Total time: 2,500 clocks.
Total time: 2,485 clocks.
Total time: 2,500 clocks.
Total time: 2,500 clocks.
Total time: 2,516 clocks.
Total time: 2,500 clocks.
Total time: 2,485 clocks.
Total time: 2,500 clocks.
Total time (BEST RUN): 2,484 clocks.

D:\Lookupperorama_r13\COLLISION_Hashliner>BenchHashingLines_XXH128.exe 200000000.KnightTours.txt
Hashing 200,000,000 lines/keys, 128 bytes each, in RAM ...
The first key has hash:
c703b0bd77
Total time: 4,204 clocks.
Total time: 3,359 clocks.
Total time: 3,360 clocks.
Total time: 3,360 clocks.
Total time: 3,359 clocks.
Total time: 3,360 clocks.
Total time: 3,360 clocks.
Total time: 3,359 clocks.
Total time: 3,375 clocks.
Total time: 3,266 clocks.
Total time: 3,349 clocks.
Total time: 3,360 clocks.
Total time: 3,375 clocks.
Total time: 3,360 clocks.
Total time: 3,390 clocks.
Total time: 3,376 clocks.
Total time: 3,375 clocks.
Total time (BEST RUN): 3,266 clocks.

D:\Lookupperorama_r13\COLLISION_Hashliner>
```
    
Collision testing:
    
Testset: "A billion Knight-Tours variants (each KT with 256 variants, the KT itself omitted) - each 128 bytes long"  
Testfile: 1000000000.KnightTours.txt (130,000,000,000 bytes)
    
The name of the game - hashing all lines and taking either 5 bytes or 6,7,8 bytes from the hash.
    
```
+------------------------+----------------------------------------------------------+
| Hasher                 |                          Collisions within first 5 bytes |
+------------------------+----------------------------------------------------------+
| XXH3_64bits v0.8.0     | 1,000,000,000 - 999,545,727 distinct lines =     454,273 |
| DoubleDeuceAES_128bits | 1,000,000,000 - 999,545,796 distinct lines =     454,204 |
+------------------------+----------------------------------------------------------+

+------------------------+----------------------------------------------------------+
| Hasher                 |                          Collisions within first 6 bytes |
+------------------------+----------------------------------------------------------+
| XXH3_64bits v0.8.0     | 1,000,000,000 - 999,998,214 distinct lines =       1,786 |
| DoubleDeuceAES_128bits | 1,000,000,000 - 999,998,213 distinct lines =       1,787 |
+------------------------+----------------------------------------------------------+

+------------------------+----------------------------------------------------------+
| Hasher                 |                          Collisions within first 7 bytes |
+------------------------+----------------------------------------------------------+
| XXH3_64bits v0.8.0     | 1,000,000,000 - 999,999,989 distinct lines =          11 |
| DoubleDeuceAES_128bits | 1,000,000,000 - 999,999,994 distinct lines =           6 |
+------------------------+----------------------------------------------------------+

+------------------------+----------------------------------------------------------+
| Hasher                 |                          Collisions within first 8 bytes |
+------------------------+----------------------------------------------------------+
| XXH3_64bits v0.8.0     | 1,000,000,000 - 1,000,000,000 distinct lines =         0 |
| DoubleDeuceAES_128bits | 1,000,000,000 - 1,000,000,000 distinct lines =         0 |
+------------------------+----------------------------------------------------------+
```
    
This is how the console looks like:
    
```
C:\test\COLLISION_Hashliner>GENERATE_Xmillion_Knight-Tours.bat 1000000000
Generating 1000000000 Knight-Tours and dumping them into file ...

C:\test\COLLISION_Hashliner>Knight-Tour_FNV1A_YoshimitsuTRIADii_vs_CRC32_TRISMUS.exe a8 1000000000  1>1000000000.KnightTours.txt

C:\test\COLLISION_Hashliner>bench7.bat 1000000000.KnightTours.txt

C:\test\COLLISION_Hashliner>Hashliner_XXH3_dump7byteshash.exe 1000000000.KnightTours.txt  1>1000000000.KnightTours.txt.xxh3.txt

C:\test\COLLISION_Hashliner>Hashliner_DDAES_dump7byteshash.exe 1000000000.KnightTours.txt  1>1000000000.KnightTours.txt.DDAES.txt

C:\test\COLLISION_Hashliner>Sandokan_QuickSortExternal_Deduplicated_4+GB_64bit_Intel.exe 1000000000.KnightTours.txt.xxh3.txt /fast /descend 3000
Sandokan_QuickSortExternal_4+GB r.3+, written by Kaze, using Bill Durango's Quicksort source.
Size of input file: 16,000,000,000
Counting lines ...
Lines encountered: 1,000,000,000
Longest line (including CR if present): 15
Allocated memory for pointers-to-lines in MB: 7629
Assigning pointers ...
sizeof(int), sizeof(void*): 4, 8
Trying to allocate memory for the file itself in MB: 15258 ... OK! Get on with fast internal accesses.
Uploading ...
Sorting 1,000,000,000 Pointers ...
Quicksort (Insertionsort for small blocks) commenced ...
/ RightEnd: 000,328,304,267; NumberOfSplittings: 0,114,284,204; Done: 100% ...
NumberOfComparisons: 34,310,536,510
The time to sort 1,000,000,000 items via Quicksort+Insertionsort was 2,848,402 clocks.
Performance: 12,045,534 Comparisons_128B_long-Per-Second i.e 24,091,068 RandomReads_128B_long-Per-Second.
Dumping the sorted data (Regime=2)...
\ Done 100% ...
Dumped 1,000,000,000 lines.
OK! Incoming and resultant file's sizes match.
Dumping the sorted data [deduplicated] ...
Dumped 999,999,989 distinct lines.
Dump time: 460,940 clocks.
Total time: 3,347,265 clocks.
Performance: 4,780 bytes/clock.
Done successfully.

C:\test\COLLISION_Hashliner>sort /R QuickSortExternal_4+GB.distinct.txt  1>1000000000.KnightTours.txt.xxh3.7bytes.2orABOVE.txt

C:\test\COLLISION_Hashliner>dir *7b*

15-Aug-21  12:28               156 1000000000.KnightTours.txt.DDAES.7bytes.2orABOVE.txt
15-Aug-21  11:32               286 1000000000.KnightTours.txt.xxh3.7bytes.2orABOVE.txt

C:\test\COLLISION_Hashliner>type 1000000000.KnightTours.txt.xxh3.7bytes.2orABOVE.txt
0,000,002       f84627e722e85e
0,000,002       f0039d0c4e4fce
0,000,002       c87c64d97df0e7
0,000,002       bb4344a5546572
0,000,002       af2f628f4b3ffb
0,000,002       a8cb8675c94610
0,000,002       a742cf83948622
0,000,002       657cb9dff2d962
0,000,002       436aef7ab54ce7
0,000,002       270fcde0563670
0,000,002       0b533d70915c51

C:\test\COLLISION_Hashliner>
```
Collision and Speed testing:    
    
```
Testfile: TERAPIG_Encyclopaedia_Judaica_(in_22_volumes)_TXT.tar (107,784,192 bytes)
Testmachine: Laptop 'Brutalitto' AMD 4800H max turbo 4.3GHz, 64GB DDR4 3200MHz, Windows 10
Hashtable: 27bit, i.e. 134,217,728 slots, greater than (107,784,192 bytes), since in case of perfect hasher - slots should be more than the keys at each position
+--------------------------+-----------------------------+----------------------------------+
| Hasher,                  | Number Of Hash Collisions = | RAW Hashing Speed (in one pass,  |
| GCC-10.1 compiler        | Distinct Keys -             | at each position) for keys       |
| -O3 -mavx                | Number Of Trees             | 4,6,8,10,12,14,16,18,36,64 bytes |
+--------------------------+-----------------------------+----------------------------------+
| DoubleDeuceAES_Gumbotron |                 135,752,271 |      204,640,573 KEYS-PER-SECOND |
| HighwayHash128 (generic) |                 135,754,873 |        6,336,146 KEYS-PER-SECOND |
| XXH3_128bits v0.8.0      |                 135,756,978 |      212,843,977 KEYS-PER-SECOND |
| wyhash final             |                 135,762,454 |      442,100,861 KEYS-PER-SECOND |
| XXH3_64bits v0.8.0       |                 135,763,366 |      290,994,033 KEYS-PER-SECOND |
| CRC32C (_mm_crc32_u32)   |                 135,764,628 |      252,599,460 KEYS-PER-SECOND |
| FNV1A_Pippip             |                 135,768,302 |      450,602,801 KEYS-PER-SECOND |
| SHA3-224                 |                 135,771,905 |          153,841 KEYS-PER-SECOND |
+--------------------------+-----------------------------+----------------------------------+
Note: The second column houses the cumulative value for all collisions, the collisions for all orders 4..64 were summed, that is.
```
Excellent speed for a lookupper! Considering the hash is 128bit, sometimes (in the future) 32bit won’t suffice!
The mini-main cycle (for keys 1..63 bytes long), delivering 200+ million key/s hash speed on Zen 2 Renoir 4800H max turbo 4.3GHz, DDR4 3200MHz.
The main usage, though, is in the 64+ range, where the speed is greater:
```
// Okay, the AVX2 mainloop (the handler of multiples of 64 bytes) code is only 40 instructions (icc 19.0.0 -O3 -mavx2):
// https://godbolt.org/z/oaW3zGcv5 
/*
        vmovdqu   ymm10, YMMWORD PTR [rdi]                      #94.43
        dec       rax                                           #89.9
        vmovdqu   ymm11, YMMWORD PTR [32+rdi]                   #95.43
        vpshufb   ymm8, ymm10, ymm0                             #101.12
        vpshufb   ymm7, ymm11, ymm0                             #100.12
        vpunpcklbw ymm9, ymm10, ymm11                           #191.12
        vpunpckhbw ymm12, ymm10, ymm11                          #192.12
        vmovdqu   YMMWORD PTR [64+rsp], ymm9                    #191.4
        vmovdqu   YMMWORD PTR [96+rsp], ymm12                   #192.4
        vpermq    ymm14, ymm7, 78                               #104.9
        add       rsi, -64                                      #494.22
        vpermq    ymm15, ymm8, 78                               #105.9
        vpunpcklbw ymm13, ymm14, ymm15                          #253.12
        vpunpckhbw ymm7, ymm14, ymm15                           #254.12
        vmovdqu   YMMWORD PTR [rsp], ymm14                      #104.1
        vmovdqu   YMMWORD PTR [32+rsp], ymm15                   #105.1
        vmovdqu   YMMWORD PTR [128+rsp], ymm13                  #253.4
        vmovdqu   YMMWORD PTR [160+rsp], ymm7                   #254.4
        vaesenc   xmm1, xmm1, XMMWORD PTR [rdi]                 #452.12
        vaesenc   xmm3, xmm3, XMMWORD PTR [rsp]                 #454.13
        vaesenc   xmm4, xmm4, XMMWORD PTR [64+rsp]              #457.12
        vaesenc   xmm3, xmm3, XMMWORD PTR [16+rsp]              #464.13
        vaesenc   xmm1, xmm1, XMMWORD PTR [16+rdi]              #462.12
        vaesenc   xmm7, xmm1, XMMWORD PTR [32+rdi]              #472.12
        vaesenc   xmm6, xmm6, XMMWORD PTR [128+rsp]             #459.12
        vaesenc   xmm4, xmm4, XMMWORD PTR [96+rsp]              #467.12
        vaesenc   xmm8, xmm3, XMMWORD PTR [32+rsp]              #474.13
        vaesenc   xmm6, xmm6, XMMWORD PTR [160+rsp]             #469.12
        vaesenc   xmm9, xmm4, XMMWORD PTR [80+rsp]              #477.12
        vaesenc   xmm3, xmm8, XMMWORD PTR [48+rsp]              #484.13
        vaesenc   xmm11, xmm7, XMMWORD PTR [48+rdi]             #482.12
        add       rdi, 64                                       #89.19
        vaesenc   xmm10, xmm6, XMMWORD PTR [144+rsp]            #479.12
        vaesenc   xmm4, xmm9, XMMWORD PTR [112+rsp]             #487.12
        vaesenc   xmm12, xmm11, xmm3                            #491.12
        vaesenc   xmm6, xmm10, XMMWORD PTR [176+rsp]            #489.12
        vaesenc   xmm13, xmm12, xmm4                            #492.12
        vaesenc   xmm1, xmm13, xmm6                             #493.12
        cmp       rax, -1                                       #89.9
        jne       ..B2.4        # Prob 82%                      #89.9
*/
```
