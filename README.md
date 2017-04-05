# ribbon-LoadBalanced
ribbon-LoadBalanced 负载均衡策略


Ribbon作为后端负载均衡器，比Nginx更注重的是请求分发而不是承担并发，可以直接感知后台动态变化来指定分发策略。它一共提供了7种负载均衡策略

这里以随机访问策略来举个栗子：

1、ribbon配置文件添加：

service-B.ribbon.NFLoadBalancerRuleClassName=com.netflix.loadbalancer.RandomRule

其中service-B是我注册到Eureka的serviceID，一共起了3个示例。

2、main类注册：

    @Bean
    @LoadBalanced
    RestTemplate restTemplate() {
        return new RestTemplate();
    }

    @Bean
    public IRule ribbonRule() {
        return new RandomRule();//这里配置策略，和配置文件对应
    }


3、Controller：

@RestController
public class ConsumerController {

    @Autowired
    private RestTemplate restTemplate;

    @Autowired  
    private LoadBalancerClient loadBalancerClient;  

    @RequestMapping(value = "/add", method = RequestMethod.GET)
    public String add(@RequestParam Integer a,@RequestParam Integer b) {
        this.loadBalancerClient.choose("service-B");//随机访问策略
        return restTemplate.getForEntity("http://service-B/add?a="+a+"&b="+b, String.class).getBody();

    }

}
