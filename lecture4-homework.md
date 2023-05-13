# 第4课 课后作业

## 第1题 Check out https://semaphore.appliedzkp.org/ and set up a starter project!

### 环境

```
os: macOS Version 13.3.1 (a) (22E772610a)
npm: 9.6.5
```

### 操作步骤

#### 1. 创建项目

```bash
$ npx @semaphore-protocol/cli@latest create my-semaphore-app --template monorepo-ethers
$ cd my-semaphore-app
```

### 2. 编译

```bash
$ npm i
$ cd apps/contracts
$ npm run compile
```

### 3. 部署

1. Add your environment variables in the `.env` file.

   ```bash
   DEFAULT_NETWORK=maticmum
   INFURA_API_KEY=xxx
   ETHEREUM_PRIVATE_KEY=xxx
   FEEDBACK_CONTRACT_ADDRESS=
   SEMAPHORE_CONTRACT_ADDRESS=0x3889927F0B5Eb1a02C6E2C20b39a1Bd4EAd76131
   OPENZEPPELIN_AUTOTASK_WEBHOOK=
   GROUP_ID=4422
   REPORT_GAS=false
   COINMARKETCAP_API_KEY=
   ETHERSCAN_API_KEY=
   ```

2. Go to the `apps/contracts` folder and deploy your contract.

   ```bash
   $ npm run deploy -- --semaphore 0x3889927F0B5Eb1a02C6E2C20b39a1Bd4EAd76131 --group 4422 --network mumbai
   ```

   报错：

   ```bash
   Internal Error: @semaphore-protocol/cli-template-monorepo-ethers@workspace:.: This package doesn't seem to be present in your lockfile; run "yarn install" to update the lockfile
       at nF.getCandidates (/Users/Jude/github-space/my-semaphore-app/.yarn/releases/yarn-3.2.1.cjs:438:4480)
       at Bd.getCandidates (/Users/Jude/github-space/my-semaphore-app/.yarn/releases/yarn-3.2.1.cjs:396:1281)
       at /Users/Jude/github-space/my-semaphore-app/.yarn/releases/yarn-3.2.1.cjs:442:7764
       at Rg (/Users/Jude/github-space/my-semaphore-app/.yarn/releases/yarn-3.2.1.cjs:395:11098)
       at le (/Users/Jude/github-space/my-semaphore-app/.yarn/releases/yarn-3.2.1.cjs:442:7744)
       at async Promise.allSettled (index 0)
       at async uo (/Users/Jude/github-space/my-semaphore-app/.yarn/releases/yarn-3.2.1.cjs:395:10390)
       at async /Users/Jude/github-space/my-semaphore-app/.yarn/releases/yarn-3.2.1.cjs:442:8292
       at async pi.startProgressPromise (/Users/Jude/github-space/my-semaphore-app/.yarn/releases/yarn-3.2.1.cjs:395:47994)
       at async ze.resolveEverything (/Users/Jude/github-space/my-semaphore-app/.yarn/releases/yarn-3.2.1.cjs:442:6285)
   npm ERR! Lifecycle script `deploy` failed with error: 
   npm ERR! Error: command failed 
   npm ERR!   in workspace: monorepo-ethers-contracts@1.0.0 
   npm ERR!   at location: /Users/Jude/github-space/my-semaphore-app/apps/contracts 
   ```

   按照提示，执行：

   ```
   $ yarn install
   ```

   操作完成后，再执行：

   ```bash
   $ npm run deploy -- --semaphore 0x3889927F0B5Eb1a02C6E2C20b39a1Bd4EAd76131 --group 4422 --network mumbai
   ```

   部署成功：

   ```bash
   Feedback contract has been deployed to: 0xDefa9ACcA46d0a0420279381aB626b42AE26aEAf
   ```

   把 Feedback contract 地址填入 .env

   ```bash
   FEEDBACK_CONTRACT_ADDRESS=0xDefa9ACcA46d0a0420279381aB626b42AE26aEAf
   ```

3. 启动App

   ```bash
   $ cd ../../
   $ npm run dev:web-app 
   ```
   
   按页面提示操作。



