# syw2plus
임진록2 파일 포맷

> [!NOTE]
> 파일 포맷 읽기 확인된 버전: 천년의 신화, 조선의 반격

## SPR

스프라이트 이미지 파일(SPR; Sprite)

이미지는 픽셀별 색상이 아닌 팔레트 인덱스를 기반으로 하고 있다. 쉽게 말해 8비트 비트맵 기반.

자체 제작 포맷을 따르는 것으로 보이기 때문에 이와 비슷한 구조를 갖는 이미지 확장자는 아직 발견하지 못했다.

`Signature`는 반드시 0x09의 값을 가져야하고, 임진록2 실행 파일에서 0x09가 아니면 읽기를 거부하는 걸로 보인다.

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
|Dummy2|`uint8_t[]`|32|0x00|더미 배열 2|
|CompressedPaletteIndexes|`uint8_t*`|||팔레트 인덱스 배열|

> [!TIP]
> `Math.floor(Width / FrameWidth)`로 X축에 존재하는 프레임의 수를 획득할 수 있다. Y축도 마찬가지.

> [!NOTE]
> 지금 기록하고 생각난 건데... `CompressedPaletteIndexes`는 동적 할당이 아니라 `NumberOfFrames`와 `CompressedSizes`를 이용해 읽어볼 수 있을 듯
> 어차피 한 프레임의 크기는 서로 같고, 압축된 크기도 같을테니까.
