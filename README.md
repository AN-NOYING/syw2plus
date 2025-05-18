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
|Offsets|`uint32_t[]`|1200||압축된 프레임의 시작되는 상대 주소 배열|
|CompressedSizes|`uint16_t[]`|600||압축된 프레임의 크기 기록 배열|
|TotalCompressedSize|`uint32_t`|4||총 압축 크기|
|Width|`uint32_t`|4||스프라이트 이미지의 가로 길이|
|Height|`uint32_t`|4||스프라이트 이미지의 세로 길이|
|Dummy2|`uint8_t[]`|32|0x00|더미 배열 2|
|CompressedPaletteIndexes|`uint8_t*`|||팔레트 인덱스 배열|

> [!TIP]
> `Math.floor(Width / FrameWidth)`로 X축에 존재하는 프레임의 수를 획득할 수 있다. Y축도 마찬가지.

### 압축 방식

팔레트 인덱스 배열(`CompressedPaletteIndexes`)은 RLE(Run Length Encoding) 방식을 변형하여 압축되어 있습니다.

투명 픽셀(0xFE)을 의미하는 팔레트 인덱스만 압축되고 있으며, [0xFE][압축된 개수]로 기록되어 있습니다. 즉, 팔레트 인덱스 데이터를 읽다가 0xFE 값을 만나면 다음 바이트 값을 확인해서 압축 해제하면 됩니다.

지금은 찾아보기 힘든 정보지만 과거 '0번 압축'이라 부르기도 하였습니다. 0번이나 255번 등을 투명색으로 했기 때문에 그렇습니다.

## YTL

임진록 타일맵 이미지 파일(YTL; Yimjinrok Tile)

Yimjinrok Tile이라는 건 그저 갖다 붙인 것일 뿐 공식 약어는 아닙니다. Y로 시작하는 게 임진록 말고는 없어보여서...

SPR 파일과 마찬가지로 매우 유사한 구조를 갖고 있고, 팔레트 인덱스 배열의 압축 방식만 조금 다릅니다.

**작성 중**
