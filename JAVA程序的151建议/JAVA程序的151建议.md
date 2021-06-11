# JAVA程序的151建议





## 不要再常量或变量中出现易混淆的字母

1. 包名全小写 

   ```
   org.companyname.com.project
   ```

2. 类名首字母全大写

   ```
   public class ClassName{ ... }
   ```

3. 常量全部大写并用下划线分割

   ```
   public static final String CONST_STR = "str";
   ```

4. 变量采用驼峰式命名法

   ```
   private String changeStr = "str";
   ```

## 莫让常量蜕变成变量

错误示例

```

//接口常量
interface Const{
	//这还是常量吗?
	public static final int RAND_CONST = new Random().nextInt();
}

```

## 三元操作符类型务必一致

错误示例

```
public class Client{
	public static void main(String [] args){
	 	int	i = 80;
	 	String s = String.valueof(i<100?90:100);
	 	String s1 = String.valueof(i<100?90:100.0);
	 	System.out.println("两者是否相等:"+s.equals(s1));
	}
}
```

```
/*由于三元操作符中两个操作数的类型不一致,此时s1对应式子的三元操作符 90(int) 被转换 90.0(float)类型*/
输出结果： false
```

### 补充：三元操作符类型转换规则

```
1.若两个操作数不可转换,则不做转换,返回为Object的类型
2.若两个操作数是明确类型的表达式（比如变量），则按照正常的二进制数字来转换
  int类型转换为long类型;long类型转换为float类型等...
3.两个操作数中一个是数字S，另一个是表达式，且其类型标示为T，那么 若数字S在T的范围内，则转换为T的类型；若S超出了T类型的范围，	 则T转换为S的类型
4.若两个操作数都是直接量数字，则返回值类型为范围较大者
  例-直接量：long类型的直接量：在整数值后面添加l或L就变成long类型的直接量
 		   float类型的直接量：在一个浮点数后添加f或F就变成float类型的直接量           
```

[【直接量】参考博客解释](http://m.hqwx.com/news/2019-12/15762050791611.html)

## 避免带有变长参数的方法重载

错误示例：

```
public class Client{
	//简单折扣计算
	public void calPrice( int price ,int discount ){
		float knowckdownPrice = price * discount  / 100.0F;
		System.out.println("简单折扣后的价格是：+formatCurrency(knowckdownPrice)");
	}
	
	//复杂多折扣计算
	public void calPrice( int price ,int ... discounts ){
		float knockdownPrice = price;
		for( int discount ,discounts){
			knockdownPrice = knockdownPrice * discount / 100;
		}
		System.out.println("复杂折扣后的价格是：+formatCurrency(knowckdownPrice)");
	}
	
	//格式化成本的货币形式
	private String formatCurrency( float price ){
		return NumberFormat.getCurrencyInstance().format(price/100);
	}
	
	public static void main(String [] args){
		Client client = New Client();
		//499元的货物,打75折
		client.calPrice(49900,75);
	}
}
```

```
/*输出结果*/
简单折扣后的价格是：￥374.25
```



## 别让null值和空值威胁到变长方法



