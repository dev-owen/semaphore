# 빠른 시작

세마포는 Node 11.14.0 버전에서 테스트 되었다. 
이는 Node 12 LTE 버전에서 실행이 될 것으로 예상되나, 가급적 Node 11.14.0 버전에서 사용하는 것을 권장한다.
만약 당신이 소스 코드를 개발하고 싶고, 여기에 `script`에 엮여있는 의존성 중 하나를 사용한다면, Node 12 사용 시 컴파일을 할 수 없다.

노드 버전을 관리하기 위해서는 [`nvm`](https://github.com/nvm-sh/nvm) 을 사용하라.

레포지토리를 클론하고, 의존성을 설치하고, 소스 코드를 빌드하라.

```bash
git clone git@github.com:kobigurk/semaphore.git && \
cd semaphore && \
npm i && \
npm run bootstrap && \
npm run build
```

**주의**: 우리는 `circuits`,`config`, 그리고 `contracts` 서브 패키지를 관리하기 위해 `lerna`를 사용한다.
이러한 디렉토리 안에서 `npm install` 을 실행하지 마라. 대신에, 메인 디렉토리에서 `npm run bootstrap`을 실행하라.

그 다음, 컴파일된 zk-SNARK 회로, 검증 키, 그리고 증명 키를 다운로드하라. (기억해야 할 점은 이러한 키들은 테스트 목적이며, 실제 운영 목적이 아니다. 여기에 유해한 낭비가 될 수 있는 것들이 안전하게 없어졌는 지에 대한 확실성은 없다.)

이 회로와 검증 키, 증명 키를 다운로드 하기 위해서는 아래 코드를 실행하라.

```bash
# 기본 디렉토리에서 시작한다

./circuits/scripts/download_snarks.sh
```

위의 파일들을 로컬에서 실행하기 위해서는 대신 다음과 같이 실행하라.

```bash
# 기본 디렉토리에서 시작한다

./circuits/scripts/build_snarks.sh
```

이 절차는 대략 45분 정도 걸릴 것이다.

Solidity 컨트랙트를 빌드하라 (당신은 `solc` v0.5.12  버전이 당신의 `$PATH`에 설치되어 있어야 한다.):

```bash
# 기본 디렉토리에서 시작한다

cd contracts && \
npm run compileSol
```

`contracts/` 디렉토리 하에서 테스트를 실행하라:

```bash
# 첫 커맨드는 머클 트리 컨트랙트를 테스트 하고
# 두 번째 테스트는 세마포 컨트랙트를 테스트 한다

npm run test-semaphore && \ 
npm run test-mt
```
