## Tests for Csharp ##

完整题目: [http://www.cnblogs.com/leotsai/p/aspnet-tests-for-juniors.html](http://www.cnblogs.com/leotsai/p/aspnet-tests-for-juniors.html)

**第1题：**请定义一个接口IQuestion，有【标题】和【问题种类】两个属性，其中【问题种类】是只读的枚举类型QuestionType，另外还有一个方法获取该问题的答案（无参，返回字符串）。
<!--0-->
	public interface IQuestion
    {
        string Title { get; set; }
        QuestionType Type { get; }
        string GetAnwser();
    }

**第2题：**请定义一个抽象类QuestionBase，实现第一题中的IQuestion接口，其中【问题种类】属性不在该抽象类中实现，而留在该抽象类的子类中实现；获取答案的方法有默认实现，返回字符串“默认答案”。
<!--0-->
	public abstract class QuestionBase : IQuestion
    {
        private string _title;
        public string Title
        {
            get { return _title; }
            set { value = _title; }
        }
        public abstract QuestionType Type { get; }
        public virtual string GetAnwser()
        {
            return "默认答案";
        }
    }

**第3题：**请定义一个TextQuestion类，继承自第2题中的QuestionBase；获取答案的方法返回字符串”文本答案”。再定义一个MultipleChoiceQuestion类，继承自第2题中的QuestionBase；获取答案的方法返回字符串”单选答案”。
<!--0-->
	public class TextQuestion : QuestionBase
    {
        public override QuestionType Type
        {
            get { return QuestionType.Text; }
        }
        public override string GetAnwser()
        {
            return "文本答案";
        }
    }

    public class MultipleChoiceQuestion : QuestionBase
    {
        public override QuestionType Type
        {
            get { return QuestionType.MultipleChoice; }
        }
        public override string GetAnwser()
        {
            return "多选答案";
        }
    }

**第4题：**假设有实体类Product定义如下：现在有一个方法从IQueryable<Product>中获取没有删除的Product列表，该方法实现如下：请完成扩展方法：WhereNotDeleted
<!--0-->
	public static IEnumerable<Product> WhereNotDeleted(this IEnumerable<Product> source)
        {
            List<Product> activeProducts = new List<Product>();
            foreach (Product product in source)
            {
                if (product.IsDeleted == false)
                    activeProducts.Add(product);
            }
            return activeProducts as IEnumerable<Product>;
        }

**第5题：**假设数据库中有User和Income两张表如下，请仔细分析下方的示例数据，然后写出SQL得到右方的查询结果。

<!--0-->
	select u.Name, I.Year, I.Month, sum(I.Amount) Income from dbo.Income I
	inner join dbo.[user] u on u.id = I.userid
	group by I.UserId, I.Year, I.Month, u.Name


**第6题：**根据第5题的数据结构，有如下两个实体类和查询结果类的定义：现有一个方法用LINQ的方式得到第5题的查询结果，该方法定义如下：请完成该方法的实现。
<!--0-->
	public static List<UserIncomeDto> GetUserIncomeDtos(List<User> users, List<Income> incomes)
        {
            var query = from i in incomes
                        join u in users on i.UserId equals u.Id
                        group i by new { i.UserId, u.Name, i.Year, i.Month } into g
                        select new
                        {
                            Name = g.Key.Name,
                            Year = g.Key.Year,
                            Month = g.Key.Month,
                            Income = g.Sum(c => c.Amount)
                        };
            List<UserIncomeDto> userIncomeDtos = new List<UserIncomeDto>();
            foreach (var d in query)
            {
                userIncomeDtos.Add(new UserIncomeDto { Name = d.Name, Year = d.Year, Month = d.Month, Income = d.Income });
            }
            return userIncomeDtos;
        }

**第7题：**在ASP.NET MVC应用程序中，假设有如下HTML表单：当该表单同步提交的时候，如何修改以上HTML和路由配置以使该请求进入下方的action中：

修改后的HTML表单：
<!--0-->
	<form action="/user/login" method="post" >
     	<input type="text" name="username" placeholder="username"/>
     	<input type="password" name="password" placeholder="password"/>
     	<input type="submit" value="login"/>
	</form>

如果不修改表单action：
<!--0-->
	<form action="/admin/mobile/user/login" method="post" >
     <input type="text" name="username" placeholder="username"/>
     <input type="password" name="password" placeholder="password"/>
     <input type="submit" value="login"/>
	</form>

需添加路由如下：
<!--0-->
	routes.MapRoute(
                "Mobile",
                "admin/mobile/{controller}/{action}",
                new { controller = "User", action = "Index"} 
            );


**第8题：**请看如下代码：请完成Validate2方法中Require方法的定义和实现，从而使得Validate2与Validate1方法实现同样的效果。

<!--0-->
	public void Require(Func<Product, string> func, string exStr)
        {
            string str = func(this);
            if (string.IsNullOrEmpty(str))
                throw new Exception(exStr);
        }