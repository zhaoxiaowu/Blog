远程调用：

连接失败： 重试多次，不行拒绝请求



远程调用rpc的时候 远程的异常如何处理？
返回 错误码+错误描述 一般RPC协议会有错误码的，比如0是正常，其他是错误码，并且附带errMsg



超时：

A rpc调用b的方法下单   b机器忙，收到消息一直未返回  导致A超时



A怎么处理

友好的提示用户即可. 正在下单,请等待...[取消下单]

然后后端,B失败自动重试(难道就一个B?不负载均衡?)或转移到其他下单服务器上.

或 A轮询问B ,

当用户点取消下单,则取消掉正在执行或已经执行的单

.先select后insert——>再参数校验是否支付过——>在执行支付；  
			 缺点：1多一次select，高并发下性能低；
			 			2.由于是两个操作，不具备原子性，存在事务问题，需要加@Transaction	 						

		2.乐观锁
			只适合update类接口；
		sql如下

 <update id="updateByLock"  parameterType="com.example.demo.dal.domain.User">
        update t_g7_test
        <set>
         
            <if test="age != null">
                age=#{age,jdbcType=INTEGER},
            </if>
            state=1
        </set>
        where id=#{id} and state=0
    </update>
    		3.mysql唯一索引	
    		4.利用redis的原子性实现分布式锁 将orderID作为key，setnx成功这进行insert/update，
    		5.或者支付系统使用uuid-生成token响应给订单系统，并把token放在redis中，订单系统依据此token来实现