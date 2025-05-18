# syw2plus
임진록2 파일 포맷

## SPR
스프라이트 이미지 파일(SPR; Sprite)
### 상수
|Name|Data Type|Size (Bytes)|Value|Desc|
|:-----|:-----|:-----|:-----|:-----|
|MAX_DUMMY_SIZE1|`int32_t`|4|1200|더미 배열 1의 크기|
|MAX_DUMMY_SIZE2|`int32_t`|4|32|더미 배열 2의 크기|
|MAX_OFFSET_SIZE|`int32_t`|4|300|오프셋 배열의 크기|
|MAX_COMPRESSED_SIZE|`int32_t`|4|300|압축 기록 배열의 크기|

### 구조
|Name|Data Type|Size (Bytes)|Value|Desc|
|:-----|:-----|:-----|:-----|:-----|
|Signature|`uint32_t`|4|0x09|시그니쳐|
|FrameWidth|`uint32_t`|4||한 프레임의 가로 길이|
|FrameHeight|`uint32_t`|4||한 프레임의 세로 길이|
|NumberOfFrames|`uint32_t`|4||총 프레임의 수|
|Dummy1|`uint8_t[]`|1200|0x00|더미 배열 1|
|Offsets|`uint32_t[]`|300||압축된 프레임의 시작되는 상대 주소 배열|
|CompressedSizes|`uint16_t[]`|300||압축된 프레임의 크기 기록 배열|
|TotalCompressedSize|`uint32_t`|4||총 압축 크기|
|Width|`uint32_t`|4||스프라이트 이미지의 가로 길이|
|Height|`uint32_t`|4||스프라이트 이미지의 세로 길이|
|Dummt2|`uint8_t[]`|32|0x00|더미 배열 2|
|CompressedPaletteIndexes|`uint8_t*`|||팔레트 인덱스 배열|

> [!NOTE]
> 지금 기록하고 생각난 건데... `CompressedPaletteIndexes`는 동적 할당이 아니라 `NumberOfFrames`와 `CompressedSizes`를 이용해 읽어볼 수 있을 듯
> 어차피 한 프레임의 크기는 서로 같고, 압축된 크기도 같을테니까.
