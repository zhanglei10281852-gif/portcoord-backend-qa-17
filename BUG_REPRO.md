# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

后台调度回收了过期任务，但没有记录回收数量，也没有进入重新分配流程。请修复回收结果在调度链路中的传播。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/portcoord-backend-qa-17
- 仓库地址：https://github.com/zhanglei10281852-gif/portcoord-backend-qa-17.git
- parent SHA：a871c547468fca670786b58fc44a6a01af36de4d

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/portcoord-backend-qa-17.git bug-repro
cd bug-repro
git checkout --detach a871c547468fca670786b58fc44a6a01af36de4d
go test ./internal/engine -run "^TestEngine_PreemptExpiredClaim$" -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/engine -run "^TestEngine_PreemptExpiredClaim$" -count=1
--- FAIL: TestEngine_PreemptExpiredClaim (0.22s)
    engine_test.go:141: expected at least 1 preemption
FAIL
FAIL	portcoord/internal/engine	0.226s
FAIL

```

stderr：

```text
warning: internal/engine/engine_test.go has type 100755, expected 100644
warning: internal/engine/engine_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/engine -run "^TestEngine_PreemptExpiredClaim$" -count=1
--- FAIL: TestEngine_PreemptExpiredClaim (0.54s)
    engine_test.go:141: expected at least 1 preemption
FAIL
FAIL	portcoord/internal/engine	0.834s
FAIL

```

stderr：

```text
warning: internal/engine/engine_test.go has type 100755, expected 100644
warning: internal/engine/engine_test.go has type 100755, expected 100644

```

## 通过条件

在题面触发条件下，公开行为必须恢复且原始异常不再出现；定向命令 go test ./internal/engine -run ^TestEngine_PreemptExpiredClaim$ -count=1、相关包测试、全量测试、race、vet 和 build 必须通过；不得删除或跳过测试，也不得绕过目标逻辑。
