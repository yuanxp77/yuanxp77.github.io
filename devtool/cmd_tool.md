spkit gen gas-spex && rm -rf ./.git/hooks/
spkit build --no-hook -H
export GOEXPERIMENT=arenas && spkit build --no-hook
export GOEXPERIMENT=arenas && spkit build mod/commission_setting --no-hook

spcli proto publish span_61779 commissionsetting.proto -n affiliateplatform.commissionsetting -f
spcli proto publish span_61779 commissionsetting.proto -n affiliateplatform.commissionsetting -f
spcli proto publish span_61779 selector.proto -n affiliateplatform.commissionruleselector -f
spcli proto publish span_61779 selector.proto -n affiliateplatform.commissionruleselector -f

CID=ID;ENV=TEST;cid=id;env=test;SP_UNIX_SOCKET=/tmp/spex.sock;GOEXPERIMENT=arenas;PFB_NAME=pfb-yxpdev
CID=ID;ENV=TEST;cid=id;env=test;SP_UNIX_SOCKET=/tmp/spex.sock;PFB_NAME=pfb-yxpdev

;PORT_computerservicev2=8080

lsof -i :9088
kill 12345
kill -9 9088

rm -f /tmp/spex.sock && socat -d -d -d UNIX-LISTEN:/tmp/spex.sock,reuseaddr,fork TCP:agent-tcp.spex.test.shopee.io:9299

【修复情况】
完全修复

【问题原因】

边界case导致，对于调高佣金率生成的普通链，本应该没有对应保护链，但在尝试获取时却成功获取到了由前一天调低佣金率生成的保护链

1. 前一天发生了调低佣金率，生成了今天0点开始生效的**普通链A+保护链A**
2. **今天0点**调高佣金率，生成 **普通链B** ，且佣金率大于等于 **保护链A** ，今天会有以上提到的3条链生效
3. 正常来说**普通链A**的佣金率小于 **保护链A** ，但按图中代码规则处理**普通链B**时，会认为**保护链A**与其是一组的，从而继续后面的佣金率校验，出现了报错

【解决方案】

降级，不return错误给用户，仅保留error log。

【影响范围】
admin端，对应item的佣金操作历史，范围较小，目前线上仅一个item符合，seller端无影响。只有频繁调高调低同一item的佣金率，且刚好在0点有调高动作才会触发，对实际的计算操作无影响。

【gitlab地址】
https://git.garena.com/shopee/marketing/affiliate/commission-plan/-/merge_requests/1943

【自测情况】
通过

apt install redis
redis-cli -h yy9mx.elasticredis.cloud.shopee.io -p 10582  -a  cache_readwrite_150080@zeJ1cL4H
redis-cli -h y91g9.elasticredis.cloud.shopee.io -p 10102
unlink affl:live:sg:commStatusDelayedQ:0

查找服务
smc -e `<env>` services ls

进入容器
smc -e `<env>` services enter `<container-name>`

查看容器日志
smc -e `<env>` services logs `<container-name>`

从服务器上下载文件
smc -e `<env>` services download `<container-name>` -s <src_dir> -d <dest_dir>

服务器上执行指令
smc -e `<env>` services run `<container-name>` '`<CMD>`'

进入sgw nginx
smc -e test services enter nginx-lb-test-sg/smc -e live services enter nginx-lb-live-sg

commission-plan canal消费
func setupCanalDataBusHandler() *canal.CanalDataBusHandler {
