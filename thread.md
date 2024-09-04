# thread

## thread 类总览

![1611108626820](thread.assets/1611108626820.png)

![1611108951145](thread.assets/1611108951145.png)

 https://www.runoob.com/w3cnote/cpp-std-thread.html 

## 构造函数

![1611110192135](thread.assets/1611110192135.png)

![1719459202408](thread.assets/1719459202408.png)



## = join joinable

https://www.cnblogs.com/adorkable/p/12722209.html

- **谁调用了这个函数？**调用了这个函数的线程对象，一定要等这个线程对象的方法（在构造时传入的方法）执行完毕后（或者理解为这个线程的活干完了！），这个join()函数才能得到返回。
- **在什么线程环境下调用了这个函数？**上面说了必须要等线程方法执行完毕后才能返回，那必然是阻塞调用线程的，也就是说如果**一个线程对象在一个线程环境调用了这个函数，那么这个线程环境就会被阻塞，直到这个线程对象在构造时传入的方法执行完毕后，才能继续往下走**，另外如果线程对象在调用join()函数之前，就已经做完了自己的事情（在构造时传入的方法执行完毕），那么这个函数不会阻塞线程环境，线程环境正常执行

![1611110508381](thread.assets/1611110508381.png)

![1611126154905](thread.assets/1611126154905.png)

## detach swap

![1611126231127](thread.assets/1611126231127.png)

## 成员函数的调用

***必须传入成员函数地址和调用的对象***

![1611128834377](thread.assets/1611128834377.png)

![1627992637203](C:\Users\wuchenhui6\AppData\Roaming\Typora\typora-user-images\1627992637203.png)

## namespace::this_thread 

![1611125851384](thread.assets/1611125851384.png)

![1611127951865](thread.assets/1611127951865.png)

# 互斥量Mutex

![1611130736570](thread.assets/1611130736570.png)

## 互斥锁mutex

![1611131293533](thread.assets/1611131293533.png)

![1611131844462](thread.assets/1611131844462.png)

## 递归锁recursive_mutex

![1611132156824](thread.assets/1611132156824.png)

递归锁出现的意义：假设存在这样一个场景，一个函数使用mutex 同时调用另外的一个函数里面有用到同一个mutex，则此时同一个互斥量被上了两次锁，导致死锁；而递归锁可以对互斥量拥有多层所有权，可以避免死锁；

**同一个线程多次占用**（递归占用次数有限，不能太多），可配合lock_guard使用，不通线程和互斥锁一致。

![1641452059800](thread.assets/1641452059800.png)

## 定时锁

![1611218398732](thread.assets/1611218398732.png)

![1611218649003](thread.assets/1611218649003.png)

# Lock 锁

## lock_guard

![1611219071765](thread.assets/1611219071765.png)

![1611219911465](thread.assets/1611219911465.png)

![1611219773123](thread.assets/1611219773123.png)

![1611219829128](thread.assets/1611219829128.png)

## unique_lock

![1611282779183](thread.assets/1611282779183.png)

![1611282857319](thread.assets/1611282857319.png)

![1611282983760](thread.assets/1611282983760.png)

## std::lock 解决死锁问题

 https://blog.csdn.net/hitxj/article/details/108439190 

![1611284221366](thread.assets/1611284221366.png)

![1611284312689](thread.assets/1611284312689.png)

![1611285982083](thread.assets/1611285982083.png)

## 消息队列

```c++
#include <list>
#include <thread>
#include <mutex>
#include<iostream>

class A
{
public:
    //把收到的消息（玩家命令）入到一个队列的线程
    void inMsgRecvQueue()
    {
        for (int i = 0;i < 100;++i)
        {
            cout << "inMsgRecvQueue()执行，插入一个元素" << i << endl;
            my_mutex.lock();
            msgRecvQueue.push_back(i);
            my_mutex.unlock();
        }
    }

    bool outMsgLULProc(int& command)
    {
        // my_mutex.lock();
        std::lock_guard<std::mutex> sguard(my_mutex);
        if (!msgRecvQueue.empty())
        {
            command = msgRecvQueue.front();
            msgRecvQueue.pop_front();
            // my_mutex.unlock();
            return true;
        }
        // my_mutex.unlock();
        return false; 
    }

    //把数据从消息队列中取出的线程
    void outMsgRecvQueue()
    {
        int command = 0;
        for (int i = 0;i < 100;i++)
        {
            bool result = outMsgLULProc(command);
            if (result == true)
            {
                cout << "outMsgRecvQueue()执行，取出一个元素" << command << endl;
                //可以考虑对命令(数据)进行处理
            }
            else
            {
                cout << "outMsgRecvQueue()执行，但是目前消息队列中为空" << i << endl;
            }
        }
        cout << "end" << endl;
    }

private:
    std::list<int> msgRecvQueue;    //容器,专门用于代表玩家发送过来的命令
    std::mutex my_mutex; //创建一个互斥量 
};

int main()
{
    A myobja;
    std::thread myOutnMsgObj(&A::outMsgRecvQueue,&myobja);
    std::thread myInMsgObj(&A::inMsgRecvQueue,&myobja);
    myInMsgObj.join();
    myOutnMsgObj.join();
    return 0;
}
```



# condition_variable

![1611130770155](thread.assets/1611130770155.png)

![1611108360117](thread.assets/1611108360117.png)

![1611108236683](thread.assets/1611108236683.png)

## 头文件中的全局变量定义const

![1611051946854](thread.assets/1611051946854.png)

![1611052105373](thread.assets/1611052105373.png)

## condition_variable

![1712829169172](thread.assets/1712829169172.png)

![1712829625248](thread.assets/1712829625248.png)

![1712829696661](thread.assets/1712829696661.png)

![1712829981043](thread.assets/1712829981043.png)

![1712830072525](thread.assets/1712830072525.png)

## 生产者消费者模型

```c++
condition_variable.h头文件
    
#include <mutex>
#include <thread>
#include <chrono>
#include <deque>
#include <condition_variable>

namespace TestConditional_variable
{
	extern std::mutex g_cvMutex;
	extern std::condition_variable g_cv;
	extern std::deque<int>g_data_deque;
	extern const int MAX_NUM;
	extern int g_next_index;

	const int PRODUCER_THREAD_NUM = 3;
	const int CONSUMER_THREAD_NUM = 3;

	void producer_thread(int thread_id);
	void consumer_thread(int thread_id);

}
```

```c++
condition_variable.cpp头文件
    
#include"Condition_variable.h"
#include<iostream>
namespace TestConditional_variable {

	 std::mutex g_cvMutex;
	 std::condition_variable g_cv;
	 std::deque<int>g_data_deque;
	 const int MAX_NUM = 30;

	 int g_next_index = 0;
	
	void producer_thread(int thread_id)
	{
		for (int i = 0; i < 4;i++)
		{
			std::this_thread::sleep_for(std::chrono::milliseconds(500));
			std::unique_lock<std::mutex>lk(g_cvMutex);
			g_cv.wait(lk, [](){return g_data_deque.size() <= MAX_NUM; });
            //wait的第二个参数为可执行的OBJ 反复执行直到返回true
			g_next_index++;
			g_data_deque.push_back(g_next_index);
			std::cout << "producer_thread" << thread_id << " producer data" << g_next_index;
			std::cout << " queue size:" << g_data_deque.size() << std::endl;
			g_cv.notify_all();
		}
	}

	void consumer_thread(int thread_id)
	{
		for (int i = 0; i < 4; i++)
		{
			std::this_thread::sleep_for(std::chrono::milliseconds(500));
			std::unique_lock<std::mutex>lk(g_cvMutex);
			g_cv.wait(lk, [](){return !g_data_deque.empty(); });
			g_next_index++;
			int data = g_data_deque.front();
			g_data_deque.pop_front();
			std::cout << "consumer_thread" << thread_id << " consumer data" << g_next_index;
			std::cout << " queue size:" << g_data_deque.size() << std::endl;
			g_cv.notify_all();
		}
	}

}
```

```c++
#include"Condition_variable.h"
#include<iostream>

int main(int argc, char *argv[])
{
	std::thread arrProducerThread[TestConditional_variable::PRODUCER_THREAD_NUM];
	std::thread arrConsumerThread[TestConditional_variable::CONSUMER_THREAD_NUM];

	for (int i = 0; i < TestConditional_variable::PRODUCER_THREAD_NUM; i++)
	{
		arrProducerThread[i] = std::thread(TestConditional_variable::producer_thread, i);
	}
	for (int i = 0; i < TestConditional_variable::CONSUMER_THREAD_NUM; i++)
	{
		arrConsumerThread[i] = std::thread(TestConditional_variable::consumer_thread, i);
	}
	for (int i = 0; i < TestConditional_variable::PRODUCER_THREAD_NUM; i++)
	{
		arrProducerThread[i].join();
	}
	for (int i = 0; i < TestConditional_variable::CONSUMER_THREAD_NUM; i++)
	{
		arrConsumerThread[i].join();
	}
	return 0;
}
```

# atomic

```c++
#include<atomic>
class atomic
class atomic_flag
```

### atmoic

![1625451096582](C:\Users\wuchenhui6\AppData\Roaming\Typora\typora-user-images\1625451096582.png)

### atomic_flag

![1625451225939](C:\Users\wuchenhui6\AppData\Roaming\Typora\typora-user-images\1625451225939.png)

# chrono 时间库

![1611295135725](thread.assets/1611295135725.png)

![1611295172011](thread.assets/1611295172011.png)

# Boost库thread

Boost的线程基本和C++自带线程使用方法一样（c++从Boost来）

```c++
#include <boost/thread.hpp> 
```

## 一个例子：

 http://zh.highscore.de/cpp/boost/ 



```c++
private:
	
	boost::thread Thread_GenerateUuid;
	boost::thread Thread_ShowUuid;

	boost::mutex mutex;
	std::queue<std::string>UuidQueue;
	void procGenerateUuid();
	void showUuid();

void GetPveDataDemo::procGenerateUuid()
{
	try{
		for (;;)
		{
			boost::this_thread::sleep_for(boost::chrono::seconds(2));
			boost::this_thread::interruption_point(); // 手动在线程中加入中断点，中断点不影响其他语句执行 当其他地方触发该断点（thread.interrupt()）再次执行到此处，抛出boost::thread_interrupted异常，若未捕捉则线程终止。 
			if (UuidQueue.size() <= 5)
			{
				boost::lock_guard<boost::mutex> lck_guard(mutex);
				std::string uuid = QUuid::createUuid().toString().toStdString();
				UuidQueue.push(uuid);
			}
		}
	}
	catch (...)
	{
		
	}
}

void GetPveDataDemo::showUuid()
{
	try{
		int cnt = 1;
		for (;;)
		{
			boost::this_thread::sleep_for(boost::chrono::seconds(2));
			boost::this_thread::interruption_point();// 手动在线程中加入中断点，中断点不影响其他语句执行 当其他地方触发该断点可从该循环中退出
			if (!UuidQueue.empty())
			{
				boost::lock_guard<boost::mutex> lck_guard(mutex);
				std::string cuuid = UuidQueue.front();
				UuidQueue.pop();
				QString UuidDisplay = "#:" + QString::number(cnt) + ": " + QString(cuuid.c_str()) + "\n";
				ui.textBrowser_UUID->append(UuidDisplay);//界面和线程写在一起了不太好	
				++cnt;
			}
		}
	}
	catch (...)
	{
		
	}
}


void GetPveDataDemo::on_pushButton_2_clicked()
{
	//Uuid线程
	if (!Thread_GenerateUuid.joinable() && !Thread_ShowUuid.joinable())	//joinable判断线程是否结束
	{
		Thread_GenerateUuid = boost::thread(boost::bind(&GetPveDataDemo::procGenerateUuid, this));
		Thread_ShowUuid = boost::move(boost::thread(boost::bind(&GetPveDataDemo::showUuid, this)));
		ui.pushButton_2->setText(QString::fromLocal8Bit("停止生成UUID"));
	}
	else
	{
		Thread_GenerateUuid.interrupt(); //向线程发送中断请求
		Thread_ShowUuid.interrupt();
		Thread_GenerateUuid.join();//join连接 detach是脱离线程 不会阻塞
		Thread_ShowUuid.join();	 //join函数，作用是等待直到线程执行结束； 阻塞当前线程

		ui.pushButton_2->setText(QString::fromLocal8Bit("开始生成UUID"));
		ui.textBrowser_UUID->clear();
		ui.textBrowser_UUID->append(QString::fromLocal8Bit("当前生产线程停止"));
		ui.textBrowser_UUID->append(QString::fromLocal8Bit("当前消费线程停止"));
	}
	//move 移动语义 将临时对象的所有权给予GenerateUuid,避免临时变量不必要的拷贝和析构
}
```

![1621663729980](thread.assets/1621663729980.png)

![1621663753004](thread.assets/1621663753004.png)

## boost:condition

条件变量的使用：

![1712042910215](thread.assets/1712042910215.png)

![1712043202600](thread.assets/1712043202600.png)

![1712043443818](thread.assets/1712043443818.png)

## TLS线程本地化：

![1621664725335](thread.assets/1621664725335.png)

![1621664909694](thread.assets/1621664909694.png)

## thread_group 线程组

boost::thread_group 实际利用list<thread>管理一组线程，方便批量管理;如批量join;不是线程池，没有线程池的自动伸缩等功能。

![1644475338552](thread.assets/1644475338552.png)

```C++
bool TestServerClient::startServer()
{
	if (!m_bStarted)
	{ 
		if (!group)
		{
			group = new boost::thread_group();
		}
		try{
			group->create_thread(boost::bind(&TestServerClient::func_1, this));
			group->add_thread(new boost::thread(boost::bind(&TestServerClient::func_2, this, "wuchenhui")));
            //创建线程会自动加入list中（无法接受参数，需要bind） add添加线程
			m_bStarted = true;
		}
		catch (std::runtime_error e)
		{
			std::string errorstr(e.what());
		}
	}
	return m_bStarted;
}

void TestServerClient::stopServer()
{
	if (m_bStarted)
	{
		group->interrupt_all();
		group->join_all();
		group = nullptr;
	}
}
```

## thread_pool

### boost的线程池概述：

https://threadpool.sourceforge.net/index.html

 threadpool是基于[boost](https://so.csdn.net/so/search?q=boost&spm=1001.2101.3001.7020)库实现的一个线程池子库 

#include <boost/thread.hpp>
#include "threadpool/pool.hpp"

![1721185903036](thread.assets/1721185903036.png)

![1721185925799](thread.assets/1721185925799.png)

![1721185933079](thread.assets/1721185933079.png)

![1721186063548](thread.assets/1721186063548.png)

### fifo策略的pool

```
boost::threadpool::pool _thread_pool;
_thread_pool.size_controller().resize(4);
_thread_pool.schedule(boost::bind(&DeviceManager::SendParkingLots, this, nBelongedPark));
//同步等待结束
_thread_pool.wait();
```

### priority优先级线程池

```C++
#include <boost/thread.hpp>
#include "threadpool/pool.hpp"



class FujianEtcInterface final:public EtcInterface
{
public:
    //...
	enum Priority_Level
	{
		LowPriority = 1000,
		MidPriority = 2000,
		HighPriority = 3000
	};//枚举声明 不定义不占内存 实际存在常量区（static 也在常量区）均可类名:访问
private:
	atomic_bool m_bStarted;
	boost::threadpool::prio_pool m_EtcThreadPool;
};


bool FujianEtcInterface::startServer()
{
	if (!m_bStarted)
	{
m_EtcThreadPool.size_controller().resize(4);//初始化线程池大小
	}
	m_bStarted = true;
	return m_bStarted;
}

//使用优先级策略的线程池
void FujianEtcInterface::AfterVehPassedOperation(const PassVehicleStatusEntry& pvse)
{
		if (m_EtcThreadPool.size() > 1)
		{		m_EtcThreadPool.schedule(boost::threadpool::prio_task_func(HighPriority, boost::bind(&FujianEtcInterface::queryEtcSignInRunnable, this, pvse)));
		}
}

void FujianEtcInterface::stopServer()
{
	if (m_bStarted)
	{
        //清除pending的task 在等待所有active和pending的task结束
		m_EtcThreadPool.clear();
		m_EtcThreadPool.wait();
	}
}
```





# Boost

https://theboostcpplibraries.com/

![1712059641529](thread.assets/1712059641529.png)

![1712059647894](thread.assets/1712059647894.png)

## boost 库之内存池

   pool每次向系统申请一大块内存，然后分成同样大小的多个小块，
  形成链表连接起来。每次分配的时候，从链表中取出头上一块，提
  供给用户。链表为空的时候，pool继续向系统申请大块内存。     

 boost.pool 库基于简单分隔存储的思想实现了一个快速、紧凑的内存池库，不仅能够管理大量的对象，还可以用作 STL 的内存分配器。在某种程度上讲，它近似于一个小型的垃圾回收机制，在需要大量分配/释放小对象时其效率很高，而且完全不需要考虑 delete。

  **pool库包含4个部分：最简单的pool、分配类实例的object_pool、单件内存池singleton_pool和可用于标准库的 pool_alloc 。**

### pool

   pool是最简单、最容易使用的内存池类，它可以返回一个简单数据类型（**POD：一个类或结构体通过二进制拷贝后还能保持其数据不变，那么它就是一个POD类型**）的内存指针。

   需要注意一点：pool只能作为普通数据类型(如 int、double等）的内存池，不能将其应用于复杂的类和对象，因为它**只分配内存，不调用构造函数**。

除非有特殊要求，否则不必对分配的内存调用 free()、purge_memory()释放， pool 会很好地管理内存。

因为 pool 在分配内存失败的时候不会抛出异常，所以实际编写代码时应该检查 malloc( )函数返回的指针，以防止空指针发生错误，不过通常这种情况极少出现。



![1685435651239](thread.assets/1685435651239.png)



```c++
{	
		boost::pool<> pool1(sizeof(int));              //1个可分配 int 的内存池
		int* p = static_cast<int*> (pool1.malloc());   //从内存池分配内存块，必须把void*转为需要的类型
		if (p != nullptr) {
			if (pool1.is_from(p))                      //判断p是否从pool1这个内存池分配的
			{
				std::cout << "p是从pool1内存池中分配的" << std::endl;
				pool1.free(p);                         //释放分配的内存块给内存池 不是释放给系统
			}
		}
		std::cout << pool1.get_requested_size() << std::endl; //4 获得每次分配的内存块大小
		for (int i = 0; i < 500; i++)
		{
			int* _p = static_cast<int*> (pool1.ordered_malloc(10)); //连续分配10块内存 从内存池中任意分配一个内存块，同时合并空闲块列表
			std::cout << _p << " " << (_p + 1) << ".." << (_p + 9) << std::endl;
		}
		//内存池对象pool1析构，所有分配的内存都被释
	}
```

![1685435779656](thread.assets/1685435779656.png)

### object_pool

![1685435879616](thread.assets/1685435879616.png)

```c++
{
		boost::object_pool<MyClass> obj_pool1;
		MyClass* myClass = obj_pool1.malloc();//分配一个原始内存块，没有初始化
		if (myClass != nullptr)
		{
			if (obj_pool1.is_from(myClass))
			{
				std::cout << "myClass是从obj_pool1内存池中分配的" << std::endl;
			}
		}
		std::cout << myClass->m_ia << std::endl;//一个随机数
		myClass = obj_pool1.construct(4, 5, 6,7);//调用构造函数，构造一个对象，可以传递参数
		std::cout << myClass->m_ia << std::endl;//4

		for (int i = 0; i < 100; ++i)
		{
			auto p_myclass = obj_pool1.construct(i, i + 1, i + 2,i+3);//construct  默认最大三个参数 但是可以自行扩展construct函数
			std::cout << p_myclass->m_ia << " " << p_myclass->m_ib << " " << p_myclass->m_ic << " " << p_myclass->m_id << std::endl;
		}

		boost::object_pool<std::string>obj_string_pool;
		for (int i = 0; i < 100; ++i)
		{
			auto p_str = obj_string_pool.construct("Hello World");
			std::cout << *p_str << std::endl;
		}
	}
```

![1685435990032](thread.assets/1685435990032.png)

![1685436651691](thread.assets/1685436651691.png)

### **singleton_pool**



![1685437092869](thread.assets/1685437092869.png)

```c++
#include "boost/pool/singleton_pool.hpp"
typedef boost::singleton_pool<struct pool_tag, sizeof(int) * 1024, boost::default_user_allocator_new_delete, boost::details::pool::default_mutex, 1024> spl;
void test::TestSingleton_pool()
{
    int* p = (int*)spl::malloc();
    if (spl::is_from(p))
    {
        std::cout << "" << std::endl;
    }
    *p = 10;
    //Sleep(500);
    //spl::purge_memory();


```



## Boost::format格式化

```c++
#include <boost/format.hpp> 

void BoostFormatTest::BoostFormat()
{
	//format返回格式化类型 basic_format
	// %数字占位符% 若非数字则非占位符 实参是%连接即可
	boost::basic_format<char> bf = boost::format("Today is %1%.%2%.%3%") % "2021" % "09" % "14";

	std::string str = (boost::format("my name is %1%,I like %2%") % "zhangsan"%"C++").str();//成员函数str 
	std::string str2 =boost::str((boost::format("my name is %1%,I like %2%") % "zhangsan"%"C++"));//成员函数str 
	std::cout << bf.str() << std::endl<< str << std::endl << str2 << std::endl; 

	boost::format fmt("This year %1%/%2%/%3%,%4% is %5%.%2%.%3%.%1%");//数字即占位符位置 可以重复使用
	fmt%"2021"%"09"%"14"%"zhangsan"%"24";
	std::cout << fmt.str() << std::endl;
	//类似于printf的使用方式
	std::string str3 =boost::str(boost::format("%d,%f,%c,%s") % 666 % 3.14%'C'%"C++");
	std::cout << str3 <<std::endl;
	//N$指定用哪个参数
	std::cout << boost::format("%3$d,%2$d,%1$d") % 2021 % 9 % 14;
}
```

![1631612894558](thread.assets/1631612894558.png)

## 序列化：



### 序列化文本：

**boost::archive::text_iarchive**

**boost::archive::text_oarchive**

可序列化基本数据类型、STL和自定义对象、指针等；继承关系的也可以；

```C++
#include<boost/archive/text_oarchive.hpp>
#include <boost/archive/text_iarchive.hpp> 
//默认支持基本数据类型和stl 但是要添加头文件
#include <boost/serialization/string.hpp> 
#include <boost/serialization/vector.hpp>
#include <boost/serialization/map.hpp>
#include <boost/serialization/list.hpp>

class Person
{
public:
	Person(){};
	Person(int _age, std::string _name) :age(_age),name(_name){
		hobbies.push_back("pingpang");
		hobbies.push_back("basketball");
		hobbies.push_back("football");
		OtherCharacter["lovecColor"]= "black";
		OtherCharacter["Pet"]= "dog";
		OtherCharacter["loveFood"]= "noodles";
	}
	int getage(){ return age; }
	std::string getname(){ return name; }
	~Person() = default;
private:
	friend class boost::serialization::access;
	template <typename Archive>//侵入式，该函数重载在里面，便于访问对象私有成员 若作为非成员函数（非侵入式）则要进行friend声明； 被动调用，写在私有里面
	void serialize(Archive &ar, const unsigned int version)
	{
		ar & age;//&重载了<<和>>，可以利用&进行对象序列化
		ar&name;
		ar&hobbies;
		ar&OtherCharacter;
	}
	int age;
	std::string name;
	std::vector<std::string> hobbies;
	std::map<std::string, std::string>OtherCharacter;
};


std::stringstream ss;
//可序列化对象和指针 STL
void TestServerClient::SerizationPerson()
{
	boost::archive::text_oarchive oa(ss);
	Person p(28,"wuchenhui");
	oa << p;
	personStr = ss.str();
	ss.str("");
	cLogger("wuchenhui")->info("personStr{}",personStr);

	boost::archive::text_oarchive oa2(ss);
	Person*p2 = new Person(27,"WCH");
	oa2 << p2;
	personStrP = ss.str();
	ss.str("");
	cLogger("wuchenhui")->info("personStr{}", personStrP);

}

void TestServerClient::LoadPerson()
{
	ss.str("");
	ss << personStr;
	boost::archive::text_iarchive ia(ss);
	Person p;
	ia >> p;
	cLogger("wuchenhui")->info("name{},age{}", p.getage(), p.getname());
	ss.str("");

	ss << personStrP;
	boost::archive::text_iarchive ia2(ss);
	Person*p2;
	ia2 >> p2;
	ss.str("");
	cLogger("wuchenhui")->info("name{},age{}", p2->getage(), p2->getname());

	
}


//文件流 text_oarchive
void TestServerClient::SerializationSave()
{
	//boost::archive::text_oarchive oa(std::cout);//cout -> ostream继承ofstream 可以流到标准输入和文件流  stringstream也是继承iostream
	std::ofstream file("archiv.txt");
	boost::archive::text_oarchive oa(file);//text_oarchive序列化为文本流
	int i = 1;
	oa << i;
	file.close();
}

void TestServerClient::SerializationLoad()
{
	std::ifstream file("archiv.txt");
	boost::archive::text_iarchive ia(file);////text_oarchive就用来从文本流恢复数据
	int i = 0;
	ia >> i;

	cLogger("wuchenhui")->info("反序列化后{}",i);
	file.close();
}
```

**继承关系的序列化：**

```C++
std::stringstream ss; 

class person 
{ 
public: 
  person() { } 
  person(int age) : age_(age) {} 
  int age() const 
  { return age_;} 
private: 
  friend class boost::serialization::access; 
  template <typename Archive> 
  void serialize(Archive &ar, const unsigned int version) 
  {  ar & age_; }
  int age_; 
}; 

class developer : public person 
{ 
public: 
  developer() { } 
  developer(int age, const std::string &language) 
    : person(age), language_(language) { } 
  std::string language() const 
  {  return language_;} 
private: 
  friend class boost::serialization::access; 
  template <typename Archive> 
  void serialize(Archive &ar, const unsigned int version) 
  { 
    ar & boost::serialization::base_object<person>(*this);//通过在子类的 serialize () 函数中用 boost::serialization::base_object () 函数访问基类实现的 
    ar & language_; 
  } 

  std::string language_; 
}; 

void save() 
{ 
  boost::archive::text_oarchive oa(ss); 
  developer d(31, "C++"); 
  oa << d; 
} 

void load() 
{ 
  boost::archive::text_iarchive ia(ss); 
  developer d; 
  ia >> d; 
} 
```



### 序列化XML:

**boost::archive::xml_iarchive**

**boost::archive::xml_oarchive**

list 序列化为xml实例：

```C++
	
    stringstream ss;
//序列化 归档xml
    //list
    boost::archive::xml_oarchive oa3(ss);
	std::list<std::string> Taricts;
	Taricts.push_back("aaa");
	Taricts.push_back("bbb");
	Taricts.push_back("ccc");
	oa3<<BOOST_SERIALIZATION_NVP(Taricts);//xml序列化 宏必不可少
	personXML = ss.str();
	ss.str("");
	cLogger("wuchenhui")->info("personStr{}", personXML);
    //map
	boost::archive::xml_oarchive oa4(ss);
	std::map<std::string,std::string> Tarictsmap;
	Tarictsmap["lovecColor"] = "black";
	Tarictsmap["Pet"] = "dog";
	Tarictsmap["loveFood"] = "noodles";
	oa3 << BOOST_SERIALIZATION_NVP(Tarictsmap);
	personXMLmap = ss.str();
	ss.str("");
	cLogger("wuchenhui")->info("personStr{}", personXMLmap);
//反序列化 从xml中恢复
    ss << personXML;
	boost::archive::xml_iarchive ia3(ss);
	std::list<std::string> Taricts;
	ia3 >> BOOST_SERIALIZATION_NVP(Taricts);
	ss.str("");

    ss << personXMLmap;
	boost::archive::xml_iarchive ia4(ss);
	std::map<std::string, std::string> Tarictsmap;;
	ia3 >> BOOST_SERIALIZATION_NVP(Tarictsmap);
	ss.str("");

```

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes" ?>
<!DOCTYPE boost_serialization>
<boost_serialization signature="serialization::archive" version="13">
<Taricts class_id="0" tracking_level="0" version="0">
	<count>3</count>
	<item_version>0</item_version>
	<item>aaa</item>
	<item>bbb</item>
	<item>ccc</item>
</Taricts>
   
 <?xml version="1.0" encoding="UTF-8" standalone="yes" ?>
<!DOCTYPE boost_serialization>
<boost_serialization signature="serialization::archive" version="13">
<Tarictsmap class_id="1" tracking_level="0" version="0">
	<count>3</count>
	<item_version>0</item_version>
	<item class_id="2" tracking_level="0" version="0">
		<first>Pet</first>
		<second>dog</second>
	</item>
	<item>
		<first>loveFood</first>
		<second>noodles</second>
	</item>
	<item>
		<first>lovecColor</first>
		<second>black</second>
	</item>
</Tarictsmap>

```

## 日期时间：

## 内存管理：

## Boost容器

### boost::bimap

双向容器

```C++
#include<boost/bimap.hpp>
void  BoostTest::test_boost_bimap()
{
	using boost::bimap;
	using namespace std;
	bimap<int, string> bi;
	//实际是两个map组装 left和right 要求key-value均唯一
	bi.insert({ 2, "dog" });
	//左视图 key int
	bi.left.insert({ 1, "cat" });
	//右视图 key string
	bi.right.insert({ "pig", 3 });
	bi.right.insert(std::make_pair("bird", 4));

	for (auto &node : bi.left)
	{
		std::cout << node.first << " " << node.second << std::endl;
	}
	for (auto &node : bi.right)
	{
		std::cout << node.first << " " << node.second << std::endl;
	}
	//查找
	auto  it = bi.left.find(2); //左视图的key为整形
	if (it != bi.left.end())
		cout << it->first << "~" << it->second << endl;

	auto it2 = bi.right.find("dog"); //右视图的key为字符串
	if (it2 != bi.right.end())
		cout << it2->first << "~" << it2->second << endl;

}
```

![1712051484931](thread.assets/1712051484931.png)

### multi_array

```C++
#include"boost/multi_array.hpp"
#include<boost/array.hpp>

void BoostTest::test_boost_mutil_array()
{
	//运行时多维数组 c++的数组必须指定二维以上维度 int a[n][10][3]
	using boost::multi_array;
	int a = 2, b = 3, c = 4;//可以cin>>输入
	//通过boost::extents函数创建多位数组
	multi_array<int, 3>arr(boost::extents[a][b][c]);
	
	//通过array创造多维数组
	boost::array<int, 3>wd2 = { a, b, c };
	multi_array<int, 3>arr2(wd2);
	
	arr2[0][0][0] = 1;
	arr2[1][2][3] = 666;
	
	//打印数组的每一个维度
	std::cout << "维度:" << arr2.num_dimensions() << ","
		<< arr2.shape()[0] << "," 
		<< arr2.shape()[1] << "," 
		<< arr2.shape()[2]<<","
		<< "元素个数" << arr2.num_elements();
	
	//遍历
	for (int i = 0; i < arr2.shape()[0]; i++)
	{
		std::cout << i << "页" << std::endl;
		for (int j = 0; j < arr2.shape()[1]; j++)
		{  
			for (int k = 0; k < arr2.shape()[2]; k++)
			{
				std::cout << i << "页" << j << "行" << k << "列" << arr2[i][j][k]<<" ";
			}
			std::cout << std::endl;
		}
	}

	//改变数组维度 原有的元素将被复制到新内存
	arr2.resize(boost::extents[4][5][6]);
	arr2[3][4][5] = 99;
	std::cout << arr2[0][0][0] << arr2[0][1][0] << arr2[3][4][5] << std::endl;
	
	boost::array<int, 3>wd = { 2, 5, 12 };//4*5*6 = 2*5*12
	arr2.reshape(wd);
	//遍历
	for (int i = 0; i < arr2.shape()[0]; i++)
	{
		std::cout << i << "页" << std::endl;
		for (int j = 0; j < arr2.shape()[1]; j++)
		{
			for (int k = 0; k < arr2.shape()[2]; k++)
			{
				std::cout << i << "页" << j << "行" << k << "列" << arr2[i][j][k] << " ";
			}
			std::cout << std::endl;
		}
	}
}
```

## Boost数据结构

###  boost::dynamic_bitset 

 	The difference is that the number of bits for `std::bitset` must be specified at compile time, whereas the number of bits for `boost::dynamic_bitset` is specified at run time. 

![1712116035027](thread.assets/1712116035027.png)

## Boost::signal

观察者模式：

 	它基于以下概念：当对应的信号被发出时，相关联的插槽即被执行。 原则上，你可以把单词 '信号' 和 '插槽' 分别替换为 '事件' 和 '事件处理器'。 不过，由于信号可以在任意给定的时间发出，所以这一概念放弃了 '事件' 的名字。 

​		 严格来说，Boost.Function 库也可以用于事件处理。 不过，Boost.Function 和 Boost.Signals 之间的一个主要区别在于，Boost.Signals 能够将一个以上的事件处理器关联至单个事件。 因此，Boost.Signals 可以更好地支持事件驱动的开发 。

​    ![1712112761953](thread.assets/1712112761953.png)



# Cplus11

## functional

![1625563637635](C:\Users\wuchenhui6\AppData\Roaming\Typora\typora-user-images\1625563637635.png)

![1625563651290](C:\Users\wuchenhui6\AppData\Roaming\Typora\typora-user-images\1625563651290.png)

## 函数适配器bind

https://blog.csdn.net/xiangbaohui/article/details/106755685

```c++
std::bind1st //绑定第一个参数  将一个二元算子转换为一元算子
std::bind2nd //绑定第二个参数  将一个二元算子转换为一元算子
std::bind    //绑定任意位置的参数
```



![1625647501927](C:\Users\wuchenhui6\AppData\Roaming\Typora\typora-user-images\1625647501927.png)

## 智能指针unique_ptr shared_ptr

https://www.cnblogs.com/lanxuezaipiao/p/4132096.html

https://www.cnblogs.com/WindSun/p/11444429.html

## #sstream

#include<sstream>
<sstream> 定义了三个类：istringstream、ostringstream 和 stringstream，分别用来进行流的输入、输出和输入输出操作。本文以 stringstream 为主，介绍流的输入和输出操作。

<sstream> 主要用来进行数据类型转换，由于 <sstream> 使用 string 对象来代替字符数组（snprintf 方式），避免了缓冲区溢出的危险；而且，因为传入参数和目标对象的类型会被自动推导出来，所以不存在错误的格式化符号的问题。简单说，相比 C 编程语言库的数据类型转换，<sstream> 更加安全、自动和直接。
**stringstream通常用来做类型转换**

```c++
void BoostFormatTest::sstreamTest()
{
	int a = 10000;
	char s[10];//10000'\0'... c串风格
	sprintf(s, "%d", a);//若误写成%f则会崩溃，类型不匹配 若s分配的内存太小则缓冲区s溢出

	// sstream 进行类型转换编译器可以自动推导类型和分配内存 更安全
	std::stringstream stream;//创建一个流
	stream << a;
	std::string stra;
	stream >> stra;
	std::cout << stra << std::endl;//10000

	float b = 3.14;
	stream.clear();//多次类型转换 每次转换都要clear；而外部缓冲区清楚 stream.str("")
	stream << b;
	std::string strb;
	stream>>strb;
	std::cout << strb << std::endl;//3.14

	std::string doublestr = "3.141592643";
	double pi;
	stream.clear();
	stream << doublestr;
	stream >> pi;
	std::cout << pi << std::endl;//3.141592643
    
    stream.str("");//清空stream 和clear转换时使用不同
    stream<<"com"<<"mul"<<"ation";
    string strcat = stream.str();//字符串拼接 commulation
   

}
```

## #files and stream

 **ofstream  ifstream fstream**

![1631696935482](thread.assets/1631696935482.png)

![1631696843743](thread.assets/1631696843743.png)

### **cin 和 cout**

cin是istream的对象，cout是ostream的对象，因此istream和ostream操作文件流和cin和cout用法完全一致。

在理解cin功能时，不得不提标准输入缓冲区。当我们从键盘输入字符串的时候需要敲一下**回车键才能够将这个字符串送入到缓冲区中**，那么敲入的这个**回车键(\r)会被转换为一个换行符\n**，这个换行符\n也会被存储在cin的缓冲区中并且被当成一个字符来计算！比如我们在键盘上敲下了123456这个字符串，然后敲一下回车键（\r）将这个字符串送入了缓冲区中，那么此时缓冲区中的字节个数是7 ，而不是6。

cin读取数据也是从缓冲区中获取数据，**缓冲区为空时，cin的成员函数会阻塞等待数据的到来，一旦缓冲区中有数据，就触发cin的成员函数去读取数据**

```c++
cin的输入
		cin>> istream重载<<: 可以连续从键盘读取想要的数据，**以空格、tab或换行作为分隔符** ;
		                     若缓冲区中第一个字符是空格、tab或换行这些分隔符时，cin>>会将其忽略并清除

		get:  1,只能将字符串读入C风格的字符串中，即char* 2,对于换行符不处理，下次读取会读入换行符打印ASCII 0
		int cin.get()          :从标准输入（键盘）输入一个字符 cin.get()
		istream& cin.get(char&c)    :读取一个字符。赋值给c
		istream& cin.get(char*s,streamsize n) n为目标空间s大小；默认以换行符结束
		istream& cin.get(char*s,streamsize n,streamsize delim) 可以指定结束符delim

		getline:1,可以将字符串读入C++风格的字符串中，即string类型 2,默认遇到’\n’时终止，并且将’\n’直接从输入缓冲区中删除掉，不会影响下面的输入处理。
		istream& cin.getline(char*s,streamsize n)
		istream& cin.getline(char*s,streamsize n,streamsize delim)
		c++字符串 #include<string>中
		istream& getline(istream&cin,string&str) 
		istream& getline(istream&cin,string&str,streamsize delim)
		
```

```c++
void BoostFormatTest::filestreamTest()
{
	std::fstream os;
	os.open("readme.txt", std::ios::out|std::ios::trunc);
	if (os.is_open())
	{
		os << "hello world!\n";
		os << "just test the file";
		os.close();//调用之后，将缓存中的数据排放出来，关闭文件；os流可用于其他文件；文件可被重新打开；
	}
	os.open("readme.txt", std::ios::in | std::ios::app);
	{
		std::string content;
		while (getline(os, content))//读到文件结束符结束
		{
			std::cout << content<<std::endl;
		}
		os.close();
	}
	std::ofstream os2("readme.txt", std::ios::out | std::ios::trunc);
	{
		if (os2.is_open())
		{
			os2 << "Hello World\n";
			os2 << "See you again";
			os2.close();
		}

	}
	/*default (1)          ifstream();
	initialization(2)    explicit ifstream(const char* filename, ios_base::openmode mode = ios_base::in);
	explicit ifstream(const string& filename, ios_base::openmode mode = ios_base::in);
	copy(3)              ifstream(const ifstream&) = delete;//禁止
	move(4)              ifstream(ifstream&& x);
	*/
	std::ifstream os3("readme.txt");//默认in模式
	{
		if (os3.is_open())
		{
			char str[100];
			while (os3.getline(str,100))
			{
				std::cout << str << std::endl;
			}
			os3.close();
		}
	}
	/*
	所有输入/输出流对象(i/o streams objects)都有至少一个流指针：

	ifstream， 类似istream, 有一个被称为get pointer的指针，指向下一个将被读取的元素。
	ofstream, 类似 ostream, 有一个指针 put pointer ，指向写入下一个元素的位置。
	fstream, 类似 iostream, 同时继承了get 和 put

	tellg() 和 tellp()
	    这两个成员函数不用传入参数，返回整数，代表当前get 流指针的位置 (用tellg) 或 put 流指针的位置(用tellp).
		seekg() 和seekp()
		这对函数分别用来改变流指针get 和put的位置。两个函数都被重载为两种不同的原型：
    seekg 和 seekp
		seekg ( pos_type position );
		seekp ( pos_type position );
		使用这个原型，流指针被改变为指向从文件开始计算的一个绝对位置。要求传入的参数类型与函数 tellg 和tellp 的返回值类型相同。

		seekg ( off_type offset, seekdir direction ); ios::beg 文件开始 ios::end      ios::cur
		seekp ( off_type offset, seekdir direction );
		使用这个原型可以指定由参数direction决定的一个具体的指针开始计算的一个位移(offset)。
		
	*/
	std::ifstream os4("readme.txt");
	{
		int l = os4.tellg();
		os4.seekg(0, std::ios::end);
		int e = os4.tellg();
		std::cout << "size is " << e - l << "Bytes" << std::endl;
		os4.close();
	}
	//二进制文件的读写  write和read
	//二进制文件和文本文件的区别：文本文件是经过编码后的文件（unicode txt mp4...）而二进制文件则未经编码 
	//对于文本文件若用二进制的方式去读则读到的数据未经编码则乱码，对于二进制数据用文本格式去读也会映射错误
	std::ofstream os5("ReadMea", std::ios::out|std::ios::trunc);
	{
		if (os5.is_open())
		{
			char str[16] = "hello";
			os5.write(str, 16);
			os5.close();
		}
	}

	std::ifstream os6("ReadMea");
	{
		if (os6.is_open())
		{
			int size = 6;
			char *buffer = new char[size];
			os6.read(buffer, size);
			os6.close();
		}
	}
}
```



## 右值引用

## using 语法糖



https://zhuanlan.zhihu.com/p/21264013

```c++
	void testusing()
	{
        //typedef  返回类型(*新类型)(参数表)
		//1.using 命名空间
		using std::map;
		//2.使用别名 类似typedef
		using fun = std::function<void(std::string)>;
		using Pfun = void(*)(std::string);
		map<std::string, fun>str2funmap;
		map<std::string, Pfun>str2Pfunmap;
		str2funmap.insert(std::make_pair("printonce", [](std::string printonce){
			std::cout << printonce << std::endl;
		}));
		str2Pfunmap["printonce"] = [](std::string printonce){
			std::cout << printonce << std::endl;
		};
		auto func = str2funmap["printonce"];
		func("hello world");
		auto pfunc = str2Pfunmap["printonce"];
		pfunc("hello world");
		// 可以别名模板
		template <typename T>
		using func_t = std::function(void(T,T));
	
		template <typename Val>
		using str_map_t = std::map<std::string, Val>;
		str_map_t<int> map1; //2013不支持
	}
```

## Lambda

表达式**用于定义并创建匿名的函数对象**，以简化编程工作。首先看一下Lambda表达式的基本构成：

​                                ![1639378359124](thread.assets/1639378359124.png)

[[函数对象参数](操作符重载函数参数)]()mutable或exception ->返回值{函数体}

 

① 函数对象参数；

[]，标识一个**Lambda的开始**，这部分必须存在，**不能省略**。函数对象参数是传递给编译器自动生成的函数对象类的构造函数的。函数对象参数只能使用那些到定义Lambda为止时Lambda所在作用范围内可见的局部变量（包括Lambda所在类的this）。函数对象参数有以下形式：

n 空。没有使用任何函数对象参数。

n =。函数体内可以使用Lambda所在作用范围内所有可见的局部变量（包括Lambda所在类的this），并且是**值传递方式**（相当于编译器自动为我们按值传递了所有局部变量）。

n &。函数体内可以使用Lambda所在作用范围内所有可见的局部变量（包括Lambda所在类的this），并且是**引用传递方式**（相当于编译器自动为我们按引用传递了所有局部变量）。

n this。函数体内可以使用Lambda所在类中的成员变量。

n a。将a按值进行传递。按值进行传递时，函数体内不能修改传递进来的a的拷贝，因为默认情况下函数是const的。要修改传递进来的a的拷贝，可以添加mutable修饰符。

n &a。将a按引用进行传递。

n a, &b。将a按值进行传递，b按引用进行传递。

n =，&a, &b。除a和b按引用进行传递外，其他参数都按值进行传递。

n &, a, b。除a和b按值进行传递外，其他参数都按引用进行传递。

int m = 0, n = 0;

[=] (int a) mutable { m = ++n + a; }(4);

   [&] (int a) { m = ++n + a; }(4);

 

   [=,&m] (int a) mutable { m = ++n + a; }(4);

   [&,m] (int a) mutable { m = ++n + a; }(4);

 

   [m,n] (int a) mutable { m = ++n + a; }(4);

   [&m,&n] (int a) { m = ++n + a; }(4);

② 操作符重载函数参数；

标识重载的()操作符的参数，没有参数时，这部分可以省略。参数可以通过按值（如：(a,b)）和按引用（如：(&a,&b)）两种方式进行传递。

③ 可修改标示符；

mutable声明，这部分可以省略。按值传递函数对象参数时，加上mutable修饰符后，可以修改按值传递进来的拷贝（注意是能修改拷贝，而不是值本身）。

④ 错误抛出标示符；

exception声明，这部分也可以省略。exception声明用于指定函数抛出的异常，如抛出整数类型的异常，可以使用throw(int)

⑤ 函数返回值；

->返回值类型，标识函数返回值的类型，当返回值为void，或者函数体中只有一处return的地方（此时编译器可以自动推断出返回值类型）时，这部分可以省略。

⑥ 是函数体；

  {}，标识函数的实现，这部分不能省略，但函数体可以为空。

## 

# Some Traitics

## volatile

​     有些变量是用volatile关键字声明的。当两个线程都要用到某一个变量且**该变量的值会被改变时，应该用volatile声明**，**该关键字的作用是防止优化编译器把变量从内存装入CPU寄存器中**。如果变量被装入寄存器，那么两个线程有可能一个使用内存中的变量，一个使用寄存器中的变量，这会造成程序的错误执行。volatile的意思是让编译器每次操作该变量时一定要从内存中真正取出，而不是使用已经存在寄存器中的值。



![1696938068620](thread.assets/1696938068620.png)



## 头文件只能声明不能定义

![1627530065752](C:\Users\wuchenhui6\AppData\Roaming\Typora\typora-user-images\1627530065752.png)

![1627539306660](C:\Users\wuchenhui6\AppData\Roaming\Typora\typora-user-images\1627539306660.png)

## static的成员函数和成员的声明和定义

声明static 定义可以不带static

![1627992424900](C:\Users\wuchenhui6\AppData\Roaming\Typora\typora-user-images\1627992424900.png)

![1627992480889](C:\Users\wuchenhui6\AppData\Roaming\Typora\typora-user-images\1627992480889.png)

## try catch

![1631532513443](thread.assets/1631532513443.png)

**C++标准异常**

![1631532893273](thread.assets/1631532893273.png)

![1631533030000](thread.assets/1631533030000.png)

![1631533072727](thread.assets/1631533072727.png)

## 编译/运行期多态

### 编译和运行期

要区分编译期和运行期，只需要区分你要考察的代码或数据是**在生成二进制可执行文件之前还是之后**生成和部署 。

比如int a [10]分配在栈空间， 虽然栈本身的变化是在程序执行时发生的，但是，因为程序是死的，所以**哪个栈帧上的哪个偏移是干什么用的，是在程序生成时就已经确定的**，也就是编译时就确定的。 

比如int*p = new int(10),分配在堆空间，new是向操作系统申请内存的关键字，但是操作系统分配内存需要根据程序的运行状态确定具体分配位置大小等，编译器无法提前得知，因此在运行期进行动态内存分配。



![1641540934041](thread.assets/1641540934041.png)

编译期主要进行类型安全检查，语法检查等；上面例子中编译器检查pA指针调用f自身有public没问题；但是在运行期pA指向了此时构造的B，迟绑定，调用了B的f函数，因此打破了private的封装性骗过了编译器。

### 虚函数：

**虚函数virtual:**作用是告知编译器不要静态链接该函数（编译期不指定），子类继承重写会根据对象类型动态链接。

**纯虚函数：** 纯虚函数是在基类中声明的虚函数，它在基类中没有定义，但要求任何派生类都要定义自己的实现方法。在基类中实现纯虚函数的方法是在函数原型后加“=0” 。包含纯虚函数的类叫做**抽象类**（也叫接口类），抽象类不能实例化出对象。纯虚函数在派生类中重新定义以后，派生类才能实例化出对象。 

**虚函数表：**对于有虚函数的类，编译器都会维护一**张虚函数表(虚表)**，对象的**前四个字节就是指向虚表的指针**(虚表指针)。
虚函数表的创建分为两种情况：

**无覆盖：** 

- 虚函数按声明顺序存在虚表中
- 在派生类中，前面是基类的虚函数，后面是派生类的虚函数

![1641542782614](thread.assets/1641542782614.png)

**有覆盖：**

- 先拷贝基类的虚函数表
- 如果派生类重写了基类的某个虚函数，就用派生类的虚函数替换虚表同位置的基类虚函数
- 跟上派生类自己的虚函数

![1641542831016](thread.assets/1641542831016.png)

 通过基类的引用或指针调用，调用基类还是派生类的虚函数，要根据运行时根据指针或引用实际指向或引用的类型确定，调用非虚函数时，则无论基类指向的是何种类型，都调用基类的函数 。

**多重继承**

![1641543765599](thread.assets/1641543765599.png)



### 函数重写/载/定义

![1641542402815](thread.assets/1641542402815.png)

**重定义**

![1641542168336](thread.assets/1641542168336.png)

**多态重写**

![1641543168198](thread.assets/1641543168198.png)



### 虚析构函数

只有当一个类被定义为基类的时候，才会把析构函数写成虚析构函数，可在子类析构之前调用基类析构，防止内存泄漏。若非基类不要加virtual 会增加内存开销（虚函数表），

## override final C++11

在类的继承中虚函数中使用：

```c++

//-------------------------override  重写
class A
{
    virtual void foo() = 0;
    void print();
}
class B :public A
{
    void foo(); //OK
    virtual foo(); // OK
    void foo() override; //OK 虚函数重写三种均可，函数若写错（如f00）编译器会报错
    void print() override;//Error 非虚函数不可以重载
}

//-------------------------final  终结继承
class Base 
{
    virtual void foo();
};
 
class A : Base
{
    void foo() final; // foo 被override（重写）并且是最后一个override，在其子类中不可以重写
    void bar() final; // Error: 父类中没有 bar虚函数可以被重写或final（终结）
};

class B final public: A // 指明B是不可以被继承的 B是继承的最终不可再被继承
{
    void foo() override; // Error: 在A中已经被final了
};
 
class C : B // Error: B is final
{
};


```

## 类型转化

### dynamical_cast

![1709606738660](thread.assets/1709606738660.png)

![1709606924386](thread.assets/1709606924386.png)

![1709607070796](thread.assets/1709607070796.png)

### **static_cast**

static_cast的转换格式：static_cast <type-id> (expression)

将expression转换为type-id类型，**主要用于非多态类型之间的转换，不提供运行时的检查来确保转换的安全性。**主要在以下几种场合中使用：

1.用于类层次结构中，基类和子类之间指针和引用的转换；
当进行上行转换，也就是把子类的指针或引用转换成父类表示，这种转换是安全的；
当进行下行转换，也就是把父类的指针或引用转换成子类表示，这种转换是不安全的，也需要程序员来保证；

2.用于基本数据类型之间的转换，如把int转换成char，把int转换成enum等等，这种转换的安全性需要程序员来保证；

3.把void指针转换成目标类型的指针，是及其不安全的；

![1709608193984](thread.assets/1709608193984.png)

![1709608275044](thread.assets/1709608275044.png)

![1709608424311](thread.assets/1709608424311.png)

### reinterpret_cast

reinterpret_cast的转换格式：reinterpret_cast <type-id> (expression)

允许将任何指针类型转换为其它的指针类型；听起来很强大，但是也很不靠谱。它主要用于将一种数据类型从一种类型转换为另一种类型。它可以将一个指针转换成一个整数，也可以将一个整数转换成一个指针，**在实际开发中，先把一个指针转换成一个整数，在把该整数转换成原类型的指针，还可以得到原来的指针值；**特别是开辟了系统全局的内存空间，需要在多个应用程序之间使用时，需要彼此共享，传递这个内存空间的指针时，就可以将指针转换成整数值，得到以后，再将整数值转换成指针，进行对应的操作。

![1709607759037](thread.assets/1709607759037.png)

![1709607766229](thread.assets/1709607766229.png)

### **const_cast**

const_cast的转换格式：const_cast <type-id> (expression)

const_cast用来将类型的**const、volatile和__unaligned属性移除**。常量指针被转换成非常量指针，并且仍然指向原来的对象；常量引用被转换成非常量引用，并且仍然引用原来的对象。

![1709608619683](thread.assets/1709608619683.png)





## shared_ptr自定义Delete 

当一个shared_ptr对象超出作用域时，其析构函数被调用，在析构函数中，将其引用计数减1，如果引用计数的值变为0，则删除关联的原始指针。即调用delete函数；

```C++
	void SharedPtr()
	{
		//std::shared_ptr<int>p1(new int[10]);  //析构时调用delete 不合理应该自己指定析构函数 c++ 17中支持动态数组<int[]>

		//可自己指定析构函数 

		//函数
		// void deleter(int*x) { delete []x;} 
		// std::shared_ptr<int>p1(new int[10],deleter);

		//函数对象 
		class deleter{
		public:
			void operator()(int*x){
				delete []x;
			}
		};
		std::shared_ptr<int>p1(new int[10], deleter());

		//lambda表达式
		std::shared_ptr<int>p1(new int[10], [](int*x){
			delete[]x;
		});
		
		//利用指定析构函数的方式可以完成一些在指针析构时执行的操作
		//1,如在析构函数中，重新资源析构函数，将资源放回pool\
		//2,在析构中执行一些退出操作后需要执行的
		std::shared_ptr<int>(new int(0), [](int*x){
			std::cout << "改作用域内代码执行完之后，想执行的操作";
			delete x;
		});

		/*
		...
		*/
		//函结束后，栈指针超出作用于要调析构函数了，会执行设定的操作
	}

}
```

## 宏

### 宏函数

|                | **#define定义宏**                                            | **函数**                                                     |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 代码长度       | 每次使用时，宏代码都会被插入到程序中。除了非常小的宏之外，程序的长度会大幅度增长 | 函数代码只出现于一个地方；每次使用这个函数时，都调用那个地方的同一份代码 |
| 执行速度       | 仅仅有运算时间，更快                                         | 存在函数的调用和返回的额外开销，所以相对慢一些               |
| 操作符优先级   | 宏参数的求值是在所有周围表达式的上下文环境里，除非加上括号，否则邻近操作符的优先级可能会产生不可预料的后果，所以**建议宏在书写的时候多些括号**。 | 函数参数只在函数调用的时候求值一次，它的结果值传递给函数。表达式的求值结果更容易预 测。 |
| 带副作用的参数 | 参数可能被替换到宏体中的多个位置，所以带有副作用的参数求值可能会产生不可预料的结果。 | 函数参数只在传参的时候求值一 次，结果更容易控制。            |
| 参数类型       | 宏的参数与类型无关，只要对参数的操作是合法的， 它就可以使用于任何参数类型。 | 函数的参数是与类型有关的，如果参数的类型不同，就需要不同的函数，即使他们执行的任务是 不同的。 |
| 调试           | 宏是不方便调试的                                             | 函数是可以逐语句调试的                                       |
| 递归           | 宏是不能递归的                                               | 函数是可以递归的                                             |

```C++
//禁止-开放第三方查费
// 宏定义中换行符\前后均不能有空格  不能写注释
#define BEGIN_ETCPAYREQUEST(direction,bofficialVer,uuid)\
    {\
		if ((direction) == 1 && (bofficialVer))\
		{\
		    PveDBUtil::updateCurrentVehicleEtcStatus((uuid), 1);\
		}\
    }

#define END_ETCPAYREQUEST(direction,bofficialVer,uuid)\
    {\
		if ((direction) == 1 && (bofficialVer))\
		{\
		    PveDBUtil::updateCurrentVehicleEtcStatus((uuid), 0); \
		}\
    }
```



### #与预定义宏

#字符串

##连接字符串



  预定义标识符：C++11 ,支持[C99](https://so.csdn.net/so/search?q=C99&spm=1001.2101.3001.7020)标准 支持以下常用宏，双下划线开始和结尾，不可取消。

__func__ 是返回所在函数的名字

__FILE__是返回当前源文件的名称

__DATE__是返回当前当前的日期

__TIME__ 是返回当前的时间

__LINE__ 是返回当前所在的行数

```C++
#define CMB(name, line) name##line
#define CMB_CON(name, line) CMB(name, line)
//必须中间宏转换 可以屏蔽# 否则发生#嵌套则不展开

#define STR(name) #name 
#define STR_CON(str) STR(str)   //# 字符串宏的中间转换宏
	void macro_funRelevant()
	{
		//这些宏会在编译时，分别转换为包含编译时间、处理的转换单元名称及当前时间的字符串。　
		std::cout << __FUNCTION__ << std::endl;  //Macro_Test::macro_funRelevant  可以在日志中调用，显示函数的调用
		std::cout << __FUNCDNAME__ << std::endl; //?macro_funRelevant@Macro_Test@@YAXXZ 函数修饰名，可帮助定位复杂的编译错误  非标准扩展
		std::cout << __FUNCSIG__ << std::endl;//void __cdecl Macro_Test::macro_funRelevant(void) 函数签名 包含函数的各种信息
		std::cout << __LINE__ << std::endl;//10行
		std::cout << __DATE__ << std::endl;//编译日期：Dec 13 2021
		std::cout << __TIME__ << std::endl;//编译时间：15:44:46
		std::cout << __FILE__ << std::endl;//文件全名：g:\qt_test&&trainning\threadstudy\testthread\testthread\MacroTest.h

		//# 转换为字符串 ##拼接 当宏中有这连个则其嵌套的宏就不会展开了 需要进行中间宏转换
		std::string ON_SCOPE_EXIT = "hello world";//变量名为EXICT24
		std::cout << STR(CMB(a, b)) << std::endl;//CMB(a,b) STR中有#因此嵌套宏不展开
		std::cout << STR_CON(CMB(a, b)) << std::endl;//ab STR_CON没有#因此嵌套宏可展开
		std::cout << STR_CON(CMB_CON(a, b)) << std::endl;//ab 转换宏屏蔽了所有# 最保险 涉及#的宏均可设置转换宏
		//
#define  ___ANONYMOUS1(type, var, line)  type  var##line
#define  __ANONYMOUS0(type, line)  ___ANONYMOUS1(type, _anonymous, line)
#define  ANONYMOUS(type)  __ANONYMOUS0(type, __LINE__)
		std::cout << STR_CON(ANONYMOUS(static int)) << std::endl; //static int _anonymous39  合并匿名对象
	   /* 
	   第一层：ANONYMOUS(static int);  -- > __ANONYMOUS0(static int, __LINE__);
		第二层：-- > ___ANONYMOUS1(static int, _anonymous, 70);
		第三层：-- > static int  _anonymous70;
		即每次只能解开当前层的宏，所以__LINE__在第二层才能被解开
		*/
	}
```

![1639394547388](thread.assets/1639394547388.png)

### #ifdef 和 #ifndef

条件编译，不同版本

![1702433913856](thread.assets/1702433913856.png)

防止重复编译

![1702433973815](thread.assets/1702433973815.png)

## RAII应用

**ON_SCOPE_EXIT**

```c++
//可让局部资源释放确定化
	class ScopeGuard
	{
	private:
		ScopeGuard(ScopeGuard const&);
		ScopeGuard& operator= (ScopeGuard const&);
	private:
		std::function<void()>onExitScope;
		bool _dismissed = false;
	public:
		explicit ScopeGuard(std::function<void()> _onExitScope)
			:onExitScope(_onExitScope), _dismissed(false)
		{}
		~ScopeGuard()
		{
			if (!_dismissed)
			{
				onExitScope();
			}
		}
		void Dismiss()
		{
			_dismissed = true;
		}
	};
#define CMB(name, line) name##line

#define SCOPEGUARD_LINENAME_CAT(name, line) name##line

#define SCOPEGUARD_LINENAME(name, line) SCOPEGUARD_LINENAME_CAT(name, line)

#define ON_SCOPE_EXIT(callback) ScopeGuard SCOPEGUARD_LINENAME(EXIT, __LINE__)(callback)

	void macro_on_scope_exit()
	{
		auto a = new int(0);
		
		ON_SCOPE_EXIT([&](){
			delete a;
			std::cout << "释放资源method1" << std::endl;
		});

		//shared_ptr 指定自定义析构函数，利用RAII进行私有资源释放
			std::shared_ptr<int>onScopeExit(new int(0), [&](int *x){
			delete x;
			std::cout << "释放资源method2" << std::endl;

		});
		//...

	}

```

![1639394735185](thread.assets/1639394735185.png)

**计算函数执行时间Trace应用**

```C++
//统计函数执行时间
	class TimeBenchmark
	{
	public:
		TimeBenchmark(const char* in = NULL, int minTime = 300)
		{
			this->minTime = minTime;
			time1 = GetTickCount();
			time2 = 0;
			func = in;
		}
		~TimeBenchmark(void)
		{
			time2 = GetTickCount();
			if (time2 - time1 >= minTime)
			{
				std::cout << func << "costs time" << int(time2 - time1) << " milliseconds over cost" << std::endl;
			
			}
			else
			{
				std::cout << func << "costs time" << int(time2 - time1) << " milliseconds less cost" << std::endl;
			}
		}
	private:
		std::string func;
		__int64 time1;
		__int64 time2;
		__int64 minTime;
	};

	void RAII_TimeBench_Test()
	{
		TimeBenchmark _tb(__FUNCTION__);
		std::this_thread::sleep_for(std::chrono::milliseconds(400));
	}
```

![1702433265818](thread.assets/1702433265818.png)



## 头文件只能声明不能定义

![1627530065752](thread.assets/1627530065752.png)



![1627539306660](thread.assets/1627539306660.png)

![1627992424900](thread.assets/1627992424900.png)

## enum(编译阶段的宏)

<img src="thread.assets/1628058231920.png" alt="1628058231920" style="zoom:200%;" />

![1628058423678](thread.assets/1628058423678.png)

**const** 常量

![1628049242363](thread.assets/1628049242363.png)

**const int 作为数组的长度（常量）**

![1628049334207](thread.assets/1628049334207.png)

**enum值作为常量在类中使用**

![1628058094946](thread.assets/1628058094946.png)

**类外使用**

```c++
class EtcManager
{
public:
	enum EtcVersion
	{
		HikEtcVersion = 0,
		ZheJiangVersion = 1
	};//枚举声明 不定义不占内存 实际存在常量区（static 也在常量区）均可类名:访问
public:
	static EtcInterference* GetInstance();
	static int GetEtcVersion();
	EtcManager() = delete;
	~EtcManager() = delete;

};
//类外  因为该枚举是一个常量，在编译的时候已经被放入了常量区。调用的时候因此不需要该枚举的变量也可以调用
if(pvse.pve.passType == 9 && EtcManager::GetEtcVersion() == EtcManager::HikEtcVersion)
		{
			EtcManager::GetInstance()->uploadPassedVehStatus4ETC(pvse.pve);
		}
```



## 嵌套类

​     **在一个类中定义的类称为嵌套类，定义嵌套类的类称为外围类** 。 对类进行嵌套通常是为了帮助实现另一个类，并避免名称冲突。 

嵌套类的访问属性和普通成员函数一样受制于private等：对于public属性嵌套类外部访问需要A::B。

 静态成员只有一份拷贝，是不依赖于类对象而存在的，**嵌套类可以访问外围类的静态成员；**
非静态成员是同对象连接到一起的，**同时this指针不会传递到嵌套类以内的成员里面去**，毕近是
两个不同的类，**所以必须通过外围类的引用或对象来访问** 。

```C++
class HikEtcManager : public EtcInterference
{
	Q_OBJECT
public:
	static HikEtcManager* GetInstance();
	//基本业务接口
	bool startServer()override;
	void stopServer()override;
	void queryFeeDeduction4ETC(const PassVehicleStatusEntry& pvse) override;//出场请求扣费
	void uploadPassedVehStatus4ETC(PassVehicleEntry&pve) override;//过车上传 视博
	public slots:
	void uploadDailyReport();
private:
	HikEtcManager(QObject *parent);
	~HikEtcManager() = default;
private:
	QTimer m_timerDailyRepor;
	QThread *m_timerDailyThread;
	static bool m_isOfficialVersion;
	static int m_uploadDailyReportDuration;
	bool m_bStarted = false;

private:

	class FeeDeduction4ETCRunnable : public QRunnable
	{
	public:
		FeeDeduction4ETCRunnable(const PassVehicleStatusEntry &param) :pvse(param) {};
		PassVehicleStatusEntry pvse;
		bool m_isLocalEtc = true;
		void run();
	};

private:
	static string buildDeductParams4ETC(PassVehicleStatusEntry&pvse);
	static HikEtcManager* m_pInstance;
	
};

#endif // HIKETCMANAGER_H
```

## struct内存对齐

 为了提高效率，计算机从内存中取数据是按照一个固定长度的。以32位机为例，它每次取32个位，也就是4个字节（每字节8个位)。字节对齐有什么好处？以int型数据为例，如果它在内存中存放的位置按4字节对齐，也就是说1个int的数据全部落在计算机一次取数的区间内，那么只需要取一次就可以了。



 有效对齐值=min{自身对齐值（成员大小），当前指定的pack值}。

**对齐准则**

  1) 结构体变量的首地址能够被其最宽基本类型成员的大小所整除；

   2) **结构体每个成员相对结构体首地址的偏移量(offset)**都是（有效对齐值）的整数倍，如有需要编译器会在成员之间加上填充字节(internal adding)；

   3) **结构体的总大小**为结构体最宽基本类型成员大小的整数倍，如有需要编译器会在最末一个成员之后加上填充字节{trailing padding}。 

```c++
 typedef struct{
      char  a;
      short b;
      char  c;
      int   d;
      char  e[3];
  }T_Test;
//*o|**|*|ooo|****|***|o  16字节

 #pragma pack(2)  //指定按2字节对齐
 struct C{
     char  b;
     int   a;
     short c;
 };
 #pragma pack()   //取消指定对齐，恢复缺省对齐
//*|o****|**       8字节

typedef struct {
    char    version;
    int16_t sid;
    int32_t len;
    int64_t time;
}__attribute__ ((packed)) Header;
// 15 用packed修饰后,变为1字节对齐,这个常用于与协议有关的网络传输中
typedef struct
{
    char  member1;
    int   member3;
    short member2;
}Family;//如下
```

**![1643102276350](thread.assets/1643102276350.png)**

## inline内联函数

函数减少了代码的重复性，减少程序的体积但是同时带来了运行性能上的开销：当前寄存器现场压栈，函数返回地址压栈保存，执行函数体，弹栈等，性能开销不小；若函数本身执行任务很简单，但是频繁调用则函数调用本身的开销可能占比整个函数执行开销较大，宏是一个解决方案，但是宏有各种安全问题；内联函数提供了更安全的解决方案。

内联函数的本质是将函数的**定义**在其调用的地方在函数编译阶段进行展开（宏是预编译），类似于宏展开；在实际的运行阶段减少普通函数调用时的开销。

表达内联**意图**的语法：

在函数**定义**返回值前添加关键字**inline**:

在类的头部声明中定义成员函数：

```C++
//普通函数在定义时添加内联符号
inline int add(int a,int b)
{
    return a+b;
}
//xxx.h
class BasicOpt
{
    public:
    baseOpt() = default;
    ~baseOpt() {}
    int add(int a,int b){
        return a+b;//类的头部声明处定义 
    }
    int mins(int a,int b);
}
inline int BasicOpt::mins(int a,int b)
{
   return a-b;    
}
```



1， 有了内联函数，就能像调用一个函数那样方便地重复使用一段代码，而不需要付出执行函数调用的额外开销，空间换时间；

2，内联函数中的代码应该只是很简单、执行很快的几条语句。如果一个函数较为复杂，它执行的时间可能上万倍于函数调用的额外开销，那么将其作为内联函数处理的结果是**付出让代码体积增加不少的代价，却只使速度提高了万分之一，这显然是不划算的**。

3，有时函数看上去很简单，例如只有一个包含一两条语句的循环，但该循环的执行次数可能很多，要消耗大量时间，那么这种情况也不适合将其实现为内联函数。需要注意的是，调用内联函数的语句前必须已经出现内联函数的**定义（即整个数体）**，而不能只出现内联函数的声明。 

 4，内联函数应该在头文件中定义，这一点不同于其他函数。编译器在调用点内联展开函数的代码时，必须能够找到 inline 函数的定义才能将调用函数替换为函数代码，而对于在头文件中仅有函数声明是不够的。 

![1645534165155](thread.assets/1645534165155.png)

## 命名空间和宏

```C++
//增量开发 最好自定义命名空间 
//一般使用namespace MYNAMESPACE{} 方式 也可以在头文件中用宏定义,各个自增量文件在包含使用
#ifndef DOCKBASECONFIG_H
#define DOCKBASECONFIG_H

#define _LY_BEGIN	namespace LYDock {
#define _LY_END		}


_LY_BEGIN
enum requestType
{
	url = 0,	//参数跟在url后面的
	json,		//json格式
	pic,		//上传图片 表单请求

	undefine = 999
};

//actionType
#define DEF_NONE				0
#define DEF_VEH_IN				1
#define DEF_VEH_OUT				2
#define DEF_CREATE_PIC_FILE		3
#define DEF_NOPLATE_IN			4
#define DEF_HISTROY_VEH_IN		5
#define DEF_HISTROY_VEH_OUT		6
#define DEF_VEH_SYNC_OUT		7		//2.15
#define DEF_SYNC_IN				8		//2.16
#define DEF_SYNC_OUT			9		//2.17


_LY_END

#endif
```

 **\#pragma region**是Visual C++中特有的预处理指令。它可以让你折叠特定的代码块，从而使界面更加清洁，便于编辑其他代码。 

```C++
#pragma region 注释性代码
...
#pragma endregion

```

![1650424687894](thread.assets/1650424687894.png)

# bitset位操作



Bitset

A bitset stores bits (elements with only two possible values: 0 or 1, `true` or `false`, ...).

**构造**

![1629167648635](thread.assets/1629167648635.png)

![1629167686553](thread.assets/1629167686553.png)

**操作符**

![1629168191899](thread.assets/1629168191899.png)

**位获取和操作**

![1629169282502](thread.assets/1629169282502.png)

![1629170095967](thread.assets/1629170095967.png)

![1629170448958](thread.assets/1629170448958.png)

![1629170480000](thread.assets/1629170480000.png)

# tuple/pair

**tuple是一个固定大小的不同类型值的集合，是泛化的std::pair**。我们也可以把他当做一个通用的结构体来用，不需要创建结构体又获取结构体的特征，在某些情况下可以取代结构体使程序更简洁，直观。std::tuple理论上可以有无数个任意类型的成员变量，而std::pair只能是2个成员，因此在需要保存3个及以上的数据时就需要使用tuple元组了。可代替struct简洁处理：如函数多个返回值，传入参数过多等。

```C++
#include<tuple>
void TestServerClient::TestTuple()
{
	//tuple 是泛华的pair 可以支持多个数据打包 比struct更方便
	//创建 打包
	std::tuple<int, int, float> tp1(1, 2, 3.1);
	std::tuple<int, std::string, double, char> tp2 = std::make_tuple(0, "b", 3.14, 'd');

	int a ; std::string b; double c; char d;
	//解包
	std::tie(a, b, c, d) = tp2;//tp2的元素分别赋值给a,b,c,d
	cLogger("wuchenhui")->info("{}{}{}{}", a, b, c, d);

	//访问get<index>(tp) 下标 0,1,2...
	std::cout << "第一个元素为:" <<std:: get<0>(tp1) << std::endl;
	std::cout << "第二个元素为:" <<std:: get<1>(tp1) << std::endl;
	std::cout << "第三个元素为:" << std::get<2>(tp1) << std::endl;
	//修改 get<index>(tp) = 
	std::get<0>(tp2) = 1;
	std::get<1>(tp2) = "wuchenhui";
	//元素个数
	size_t num = std::tuple_size<decltype(tp2)>::value;
	//元祖拼接 
	auto tuplecombine = std::tuple_cat(tp1, tp2);
}

#include<utility>
void TestServerClient::TestPair()
{
	//pair是tuple的特化 两个
	//创建 绑定
	std::pair<std::string, int>pair1("age", 27);
	auto pair2 = std::make_pair("pet", "dog");
	std::pair <std::string, int>pair3 = pair1;
	//解绑 
	std::string str1, str2;
	std::tie(str1, str2) = pair2;
	//访问修改
	pair1.second = 28;
	std::string str3 = pair1.first;
}
```





# RapidJson:

​        RapidJSON是腾讯开源的一个高效的C++ JSON解析器及生成器，它是只有头文件的C++库。RapidJSON是跨平台的，支持Windows, Linux, Mac OS X及iOS, Android。它的源码在https://github.com/Tencent/rapidjson/，稳定版本为2016年发布的1.1.0版本。

![1650533081368](thread.assets/1650533081368.png)

```C++
void TestServer::JsonParseAndCombine()
{
	/* 
	    Value 值的类型	
		kFalseType = 1,      false
		kTrueType = 2,       true
		kObjectType = 3,     object
		kArrayType = 4,      array 
		kStringType = 5,     string
		kNumberType = 6      number
	*/
	rapidjson::Value name("zhangsan");//自动推导
	rapidjson::Value Pet(rapidjson::kStringType);//指定类型
	Pet.SetString("Cat");
	rapidjson::Value age;//默认是null类型
	age.SetInt(28);//改变类型并赋值；setInt SetString SetArray SetObject SetDouble SetBool...

	//kObject
	rapidjson::Document d;//null
	d.SetObject();
	d.AddMember("Pet", Pet, d.GetAllocator());
	d.AddMember("age", age, d.GetAllocator());
	//转移语义（Move Semantics）
	rapidjson::Value JsonObj(rapidjson::kObjectType);
	//类似C++中移动语义 PushBack()、AddMember()等转移函数也采用移动语义
	JsonObj.AddMember("name", name, d.GetAllocator());//移动语义 name此时null
	int kType = name.GetType();
	JsonObj.AddMember("height", rapidjson::Value().SetInt(182), d.GetAllocator());//临时对象不能转换为正常引用
	JsonObj.AddMember("weight", rapidjson::Value(80).Move(), d.GetAllocator());//可以采用匿名对象构造或Move函数
	JsonObj.RemoveMember("name");//重载char*和Value

	std::string slogan = "Work hard study and day day up";
	rapidjson::Value Slogan;
	Slogan.SetString(slogan.c_str(), slogan.size(), d.GetAllocator());//coyp-string 调用有分配器的深拷贝
	JsonObj.AddMember("slogan", Slogan, d.GetAllocator());
	

	rapidjson::Value Color;
	Color.SetString("Yellow");// const-string 简单地储存字符串的指针 编译器自动推导长度等 要求有安全的声明周期或字面量

	//kArray 
	rapidjson::Value a(rapidjson::kArrayType);
	rapidjson::Document::AllocatorType& allocator = d.GetAllocator();
	for (int i = 5; i <= 10; i++)
		a.PushBack(i, allocator);   // 可能需要调用 realloc() 所以需要 allocator
	// 流畅接口（Fluent interface）
	a.PushBack(11, allocator).PushBack(12, allocator);
	a.PopBack();//remove the last member

	d.AddMember("array", a, allocator);
	d.AddMember("obj", JsonObj, allocator);

	std::string json;
	rapidjson::StringBuffer buffer;
	rapidjson::Writer<rapidjson::StringBuffer> writer(buffer);
	d.Accept(writer);
	json = buffer.GetString();

	//解析
	rapidjson::Document d_parse;
	if (d_parse.Parse(json.c_str()).HasParseError())
	{
		std::cout << "Json解析失败" << std::endl;
	}
	for (auto& m : d_parse.GetObject())
		std::cout << std::string(m.name.GetString()) << std::endl;

	if (d_parse.HasMember("Pet") && d_parse["Pet"].IsString())
	{
		std::cout << "Parse Pet is" << d_parse["Pet"].GetString() << std::endl;
	}

	for (auto& iter : d_parse.GetObject())//引用 浅拷贝 move
	{
		std::string tmp(iter.name.GetString());
		if (std::string(iter.name.GetString()) == "Pet")
		{
			std::cout << iter.value.GetString() << std::endl;
		}
		else if (std::string(iter.name.GetString()) == "age")
		{
			std::cout << d_parse["age"].GetInt() << std::endl;
		}
		else if (std::string(iter.name.GetString()) == "array"&&iter.value.IsArray())
		{
			auto ValueArray = iter.value.GetArray();
			for (auto &it : ValueArray)
			{
				std::cout << it.GetInt()<<" ";
			}
			std::cout << std::endl;
		}
		else if (iter.value.IsObject())
		{
			for (auto&it : iter.value.GetObject())
			{
				std::string name = std::string(it.name.GetString());
				std::string value = it.value.IsInt() ? std::to_string(it.value.GetInt()) : std::string(it.value.GetString());
				std::cout << name << ":" << value<<std::endl;
			}
		}
		else
		{
			std::cout << iter.name.GetString() << iter.value.GetString() << " ";
		}
	}
}
```

![1650533141550](thread.assets/1650533141550.png)


# 编程思想：

状态机编程思想：

框架式编程思想：

模块化编程思想：

# Release和Debug

​    debug模式下自动选择已禁用 (/Od)；release模式下自动选择 使速度最大化 (/O2) ，这种速度最大化，会导致Release版本调试出现有些代码被精简掉，有些代码变量在前后之间。

​     Release模式下的设置主要是应用于软件的发布，是不携带任何的调试信息，并且程序进行了优化，有的语句已经被优化掉，所以无法进行调试。所以只要关闭优化以及允许生成调试信息即可 。

![1645687064795](thread.assets/1645687064795.png)

## 关于pdb文件：

PDB文件的介绍

PDB（Program Data Base），意即程序的基本数据，是VS编译链接时生成的文件。DPB文件主要存储了VS调试程序时所需要的**基本信息，主要包括源文件名、变量名、函数名、FPO(帧指针)、对应的行号等等**。因为存储的是调试信息，所以一般情况下PDB文件是在Debug模式下才会生成。

PDB文件的调用过程

模块(Module)，EXE和DLL都可以称之为模块，因为它们都有自已**独立的Stack**，所以我们在调试程序时，可以在Call Stack窗口查看到所有调用的Module Name。并且可以右键查看相应模块的sybmol Load Information，即该模块调用的PDB文件路径的过程。

**每个模块被载入的时候，其相同名字的PDB文件同时被载入。**所以Debug模式下，不仅因为**代码没有优化**，同时因为要**载入PDB文件**，所以Debug模式下的程序执行速度非常慢。

每个模块只会生成一个相同名字的PDB文件，并且模块生成的同时，会校验PDB文件生成GUID记录在模块内。这是因为调试时，调试器强制要求每个模块必须和PDB文件保持一致。实验过程中，用之前生成的PDB文件替换当前生成的PDB文件时，Debug窗口会显示No symbols loaded.

**PDB文件中记录了源文件路径的相关信息**，**所以在载入PDB文件的时候，就可以将相关调试信息与源码对应**。这样可以可视化的实时查看调试时的函数调用、变量值等相关信息。模块当中记录的PDB文件是绝对路径。所以只要模块在当前电脑上载入，调试器自然地会根据模块当中的路径信息找到相应PDB文件并载入。同样PDB文件中记录的源文件路径也是绝对路径，所以PDB文件只要在当前电脑上载入，调试进入相应模块时，都能够匹配到记录的源文件，然后可视化地查看相应信息。

**dmp文件以及使用：**
dmp文件就是当程序产生异常时，用来记录当时的程序状态信息（例如堆栈的状态）,用于程序开发定位问题，是进程的内存镜像，可以把程序的执行状态通过调试器保存到dump文件中。Dump 文件是用来给驱动程序编写人员调试驱动程序用的，这种文件必须用专用工具软件打开，如：WinDbg ,VisualStudio。当我们的程序发布出去之后，在客户机上是无法跟踪自己代码的 BUG 的，我们可以通过 .dmp 文件把出现 BUG 的情况再现，然后再现客户环境 (包括堆栈调用等情况)，设置源码调试路径，可以找到出现 BUG 的语句。

**利用VS调试dmp定位问题：**

需要将现场的dmp文件、pdb、exe以及生成的dll模块都拷贝到开发机同一个文件下（尽量放到源码工程目录）。

![1645688086658](thread.assets/1645688086658.png)

用VS打开dmp文件，选择好pdb符号表文件目录

![1645688453500](thread.assets/1645688453500.png)



 ![1645688552594](thread.assets/1645688552594.png)

## Windbg

![1709710725576](thread.assets/1709710725576.png)

![1709710923025](thread.assets/1709710923025.png)

![1709710990870](thread.assets/1709710990870.png)

![1709711295645](thread.assets/1709711295645.png)

![1709711357633](thread.assets/1709711357633.png)

![1709711554004](thread.assets/1709711554004.png)

![1709711913969](thread.assets/1709711913969.png)

![1709712247275](thread.assets/1709712247275.png)

![1709712441652](thread.assets/1709712441652.png)

# 编译错误：

1,不允许指针指向不完整的类类型。

cpp类只有声明没有定义，比如前置声明一个类，而又用到这个类的成员变量或成员函数。

只要在报错的文件里include的这个类的头文件即可。

# 第三方库

## 关于 head-only 库

​     head-only库是一种只有头文件、没有任何源文件的库。即这个库的所有功能和实现都直接写在头文件，使用者只需要引入相应的头文件，即可使用这个库的所有功能。举个例子，Boost库就是以head-only的形式分发的。 

关于ABI (Application Binray interface ）兼容：与编译器 硬件架构 操作系统等均有关。

https://blog.csdn.net/NP_super/article/details/109669081



关于head-only

https://zhuanlan.zhihu.com/p/654155499

![1700741788206](thread.assets/1700741788206.png)

## spdlog（speedlog）

 pdlog就是一个开源日志库，支持跨平台(Windows、Linux、Mac、Android)，主要开发语言是C++ ,主打高性能。

    spdlog可以分成三级结构，从上而下是logger registry、logger、sink，其各自功能如下：
  **logger registry(日志管理器)：**负责管理所有的logger，用户建立的所有logger都会在registry处进行登记然后统一管理
  **logger(日志记录器)：**是用户直接操作的对象，通过操作logger进行日志逻辑的生成
  **sink(日志记录器槽)：**受logger控制，执行具体的动作(动作包括写入日志文件/输出到控制台)



![1700571334941](thread.assets/1700571334941.png)

![1700647101490](thread.assets/1700647101490.png)

![1700571792475](thread.assets/1700571792475.png)

![1700571796856](thread.assets/1700571796856.png)

![1700571803620](thread.assets/1700571803620.png)



headonly全部头文件实现，直接引入项目即可

![1700647131149](thread.assets/1700647131149.png)

![1700572058743](thread.assets/1700572058743.png)

![1700647305154](thread.assets/1700647305154.png)

![1700572134704](thread.assets/1700572134704.png)

https://www.cnblogs.com/fortunely/p/16830696.html#%E5%AD%90%E7%BA%BF%E7%A8%8B%E5%BE%AA%E7%8E%AF

```c++
//--------------------------------------MyLog.h

#ifndef MYLOG_H
#define MYLOG_H

#include"spdlog/logger.h"
void InitLog(const char*moduleName);
void UnInitLog();
std::shared_ptr<spdlog::logger> getLogger(const std::string& loggerName);

#endif // !MYLOG_H


//------------------------------------MyLog.cpp

#include "Mylog.h"
#include <QString>
#include <QDir>
#include "spdlog/spdlog.h"
#include "spdlog/sinks/base_sink.h"
#include "spdlog/sinks/rotating_file_sink.h"
#include "spdlog/sinks/msvc_sink.h"
#include <mutex>
#include <memory>
#include "spdlog/fmt/bundled/ostream.h"
#include "spdlog/common.h"

#include"spdlog/async.h"
#include"spdlog/async_logger.h"
#include<QDateTime>
#include <QCoreApplication>
#include <QDir>


std::vector<spdlog::sink_ptr> sinks;
std::recursive_mutex loggerMutex;
std::recursive_mutex asynloggerMutex;
std::recursive_mutex synloggerMutex;
std::string defaultPattern = "[%t %Y-%m-%d %H:%M:%S.%e]<%L|%n> %v";
std::string defaultAsyPattern = "[%t %Y-%m-%d %H:%M:%S.%e]<%n|%L> %v";
//%t 线程号 %Y年 %m月 %d日 %H时 %M分 %S秒 %e毫秒   %L 日志登记缩写（I D...）  %n 日志名称   %v 实际日志内容

void InitLog(const char*LogName)
{
	//sink 具体的日志记录器槽 进行最终实际输出 st单线程版本 mt多线程版本
	sinks.push_back(std::make_shared<spdlog::sinks::wincolor_stdout_sink_mt>());//std 标准输出sink 
	sinks.push_back(std::make_shared<spdlog::sinks::msvc_sink_mt>()); //msvc 多线程版本sink


	QString appPath = QCoreApplication::applicationDirPath();
	QString logPath = QDir::toNativeSeparators(QDir::cleanPath(appPath + QDir::separator() + LogName));
	QDir dir(logPath);
	if (!dir.exists(logPath))
	{
		dir.mkpath(".");
	}
	QString logfileName = logPath + QDir::separator()+"LogTest_" + QDateTime::currentDateTime().toString("yyyy_MM_dd_hh_mm_ss") + "_index_.log";
	sinks.push_back(std::make_shared<spdlog::sinks::rotating_file_sink_mt>(logfileName.toStdString(),1024 * 1024 * 4, 15, true));
}

void UnInitLog()
{
	std::lock_guard<std::recursive_mutex> guard(loggerMutex);
	spdlog::drop_all();
}


std::shared_ptr<spdlog::logger> getLogger(const std::string& loggerName)
{
	std::lock_guard<std::recursive_mutex> guard(loggerMutex);
	auto logger = spdlog::get(loggerName);//获取指定名称的logger进行使用 
	//register <name, logger> 将日志对象和其名称对应起来，后面使用的时候可以直接通过名称获取对应的日志对象
	if (logger == nullptr)
	{
		//logger 日志记录器
		logger = std::make_shared<spdlog::logger>(loggerName, begin(sinks), end(sinks));//sinks 由logger控制 执行具体输出动作
		//set_pattern 和 set_level等输出格式一般可以在sink指定 logger直接设置实质是遍历sinks设置
		logger->set_pattern(defaultPattern);//logger 设置输出格式
		logger->set_level(spdlog::level::level_enum::trace);
		//logger设置日志等级 开发阶段可设置trace debug和trace便于打印 发布阶段可调整
		//默认info等级后则低于info等级不进行打印
		// 日志等级从小到大 trace debug info(默认) warn err critical off  

		//设置日志刷新策略 默认在程序退出时才刷新 此处采用按照日志严重等级刷新高于这个level触发时刷新
		logger->flush_on(spdlog::level::level_enum::info);
		//spdlog::details::registry::instance().flush_every(std::chrono::milliseconds(500)); //每隔500ms刷新一次缓存spdlog::register_logger(logger);//register_logger 日志管理器 管理所有logger 退出前不会销毁
	}
	return logger;
}

std::shared_ptr<spdlog::logger> getAsyncLogger(const std::string& loggerName)
{
	std::lock_guard<std::recursive_mutex> guard(asynloggerMutex);
	std::string  asynloggerName = "asyn_"+loggerName;
	auto asylogger = spdlog::get(asynloggerName);//获取指定名称的logger进行使用 
	if (asylogger == nullptr)
	{
		QString appPath = QCoreApplication::applicationDirPath();
		QString logPath = QDir::toNativeSeparators(QDir::cleanPath(appPath + QDir::separator() + "AsyncLog"));
		QDir dir(logPath);
		if (!dir.exists(logPath))
		{
			dir.mkpath(".");
		}
		QString logfileName = logPath + QDir::separator() + "LogTest_" + QDateTime::currentDateTime().toString("yyyy_MM_dd_hh_mm_ss") + "_index_.log";

		spdlog::init_thread_pool(8192 * 2, 4);//设置异步循环队列缓存的大小 和 线程数 默认（8192，1）
		//异步 多线程 阻塞模式  非阻塞模式create_async_nb  日志名称 日志文件路径
		//默认为阻塞模式，非阻塞即是循环缓存队列满的时候丢弃最老的日志插入新的；阻塞即是队列满位后条件变量等待队列pop有空位在插入。
		asylogger = spdlog::create_async<spdlog::sinks::rotating_file_sink_mt>(asynloggerName,logfileName.toStdString(), 1024 * 1024 * 4, 15, true);
		asylogger->set_pattern(defaultPattern);
		asylogger->set_level(spdlog::level::level_enum::trace);
		
		asylogger->flush_on(spdlog::level::level_enum::info);
		//spdlog::register_logger(asylogger);  create时即进行了登记 重复登记会崩溃
		
	}
	return asylogger;
}



//----------------------------------TEST

void test()
{
  InitLog("MyLog");
	for (int i = 0; i < 200000; i++)
	{
	   std::thread t1([=]() {
		   getLogger("wuchenhui")->info("测试日志第{} level:{}", i, "info");
		   getLogger("wuchenhui")->error("测试错误：{}", i, "error");
		   getLogger("wuchenhui")->critical("测试crital：{}", "critical");
		   getLogger("wuchenhui")->warn("测试警告：{}", "warn");
		   getLogger("wuchenhui")->trace("测试追踪：{}", "trace");
		   getLogger("wuchenhui")->debug("测试debug：{}", "debug");
	   });
	   
	   std::thread t2([=]() {
		   getAsyncLogger("wuchenhui")->info("测试日志第{} level:{}", i, "info");
		   getAsyncLogger("wuchenhui")->error("测试错误：{}", i, "error");
		   getAsyncLogger("wuchenhui")->critical("测试crital：{}", "critical");
		   getAsyncLogger("wuchenhui")->warn("测试警告：{}", "warn");
		   getAsyncLogger("wuchenhui")->trace("测试追踪：{}", "trace");
		   getAsyncLogger("wuchenhui")->debug("测试debug：{}", "debug");
	   });
	   t1.join();
	   t2.join();
	}
	UnInitLog();
}

```

![1700572549874](thread.assets/1700572549874.png)

![1700572674481](thread.assets/1700572674481.png)

![1700647464471](thread.assets/1700647464471.png)



修改日志文件名输出格式

![1700572760612](thread.assets/1700572760612.png)

## fmt 字符串格式化

 fmt 库是一个开源的 C++ 格式化库，它提供了一种简洁、安全和[高效的](https://so.csdn.net/so/search?q=高效的&spm=1001.2101.3001.7020)方式来进行字符串格式化。
该库的设计目标是提供与 Python 的[字符串格式化](https://so.csdn.net/so/search?q=字符串格式化&spm=1001.2101.3001.7020)语法类似的功能，同时保持 C++ 的类型安全性和性能，在C++20中引入标准。

![1700740807704](thread.assets/1700740807704.png)

![1700740824951](thread.assets/1700740824951.png)

```c++
// header-only  方式使用需要定义宏FMT_HEADER_ONLY
#define FMT_HEADER_ONLY 
#include "fmt/core.h"
#include <vector>
#include <list>
#include <set>
#include<queue>
void LogTest::Testfmt()
{
	//{}作为占位符 无关类型
	//打印 fmt::print 
	fmt::print("hello {}\n", "world");
	fmt::print("{1} + {0} ={2}\n",1,2,3);//指定占位符位置 不指定则 0 1 2 3...

	//参数格式化:可以要按指定格式替换指定位置的参数，用{ 位置:格式 } 的形式表示替换内容。
	fmt::print("{0:*<10}\n", "hello");//<右填充 文本在左边 右边*填充 总共长度为10
	fmt::print("{0:*>10}\n", "hello");//左填充 文本在右边
	fmt::print("{0:*^10}\n", "hello");//两侧填充
	fmt::print("{0:>10}\n","hello");//左边填充空格
	fmt::print("{0:>{1}}\n {2:>10}\n", "hello",20,"world");//动态指定长度

	//精度格式化 
	// .n 格式化为长度n
    fmt::print("{0:.4}\n", "hello");
	fmt::print("{0:.4}\n", 3.1415926);//3.142
	//.nf 把小数部分格式化为长度n
	fmt::print("{0:.4f}\n", 3.14);//3.1400
	fmt::print("{0:.{1}f}\n", 3.14,8);//动态指定精度8位
	//+表示显示符号
	fmt::print("{0:+},{1:+}\n", -20, 20);
	//d格式化10进制 b二进制 x16进制  加#可显示进制符号 加数字可限制长度补0 20 10100 14 20  0b10100 0x14 00000020
	fmt::print("{0:d} {0:b} {0:x} {0:#d}  {0:#b} {0:#x}  {0:08d} \n", 20);

	//格式化内容到字符串 format ->std::string
	auto fmtstr = fmt::format("my name is {} and I {} years old\n", "zhangsan", 23);
	std::cout << fmtstr << std::endl;
	auto fmtstr2 = fmt::format("my name is {0:*^10} and I {1:+} years old,I konw Pi equals {2:.4f}\n", "zhangsan", 23,3.1415926);
	std::cout << fmtstr2 << std::endl;

	//容器元素格式化 join 返回
	std::string str3 = "1234567";
	fmt::print("{}\n", fmt::join(str3, ","));

	auto fmtstr4 = fmt::format("{}",fmt::join(str3, "*"));
	std::cout << fmtstr4 << std::endl;

	std::vector<char>vec { 'a','b','c','d' };
	auto fmtstr5 = fmt::format("{}", fmt::join(vec, "^"));
	std::cout << fmtstr5 << std::endl;

	std::set<char>set{ 'a','b','c','d' };
	auto fmtstr6 = fmt::format("{}", fmt::join(set, "%"));
	std::cout << fmtstr6 << std::endl;

	std::deque<char>q(6, 'a');
	auto fmtstr7 = fmt::format("{}", fmt::join(q, "^"));
	std::cout << fmtstr7 << std::endl;
}
```

![1700740955153](thread.assets/1700740955153.png)

## nlohman json

https://zhuanlan.zhihu.com/p/597612576

![1700813675778](thread.assets/1700813675778.png)

![1700814651608](thread.assets/1700814651608.png)

```c++
#include"nlohmann/json.hpp"
#include<string>
void LogTest::TestnlohmannJson()
{
	//https://www.cnblogs.com/lidabo/p/17015911.html
	using namespace nlohmann;
	//basic_value 创建 json
	json j1;
	j1["name"] = "zhangsan";
	j1["age"] = 23;
	j1["man"] = true;
	j1["child"] = {"zhangsi","zhangwu"};
	j1["behavior"]["funny"] = "gigiigi";
	j1["behavior"]["worry"] = "worry";
	j1["wife"] = { {"name","lili"},{"age",20},{"man",false} };

	json j2 = {
		{"name" ,"zhangsan"},
	    {"age", 23},
	    { "man" , true },
		{ "child" ,{"zhangsi","zhangwu"} },
		{ "behavior" ,{ {"funny","gigiigi" },{"worry","worry"} }},
		{"wife",{ { "name","lili" },{ "age",20 },{ "man",false } }}
	};
	std::cout << j1 << std::endl;
	std::cout << j2 << std::endl;
	auto name = j2["name"].get<std::string>();
	std::cout << name << std::endl;
	auto sex = j2["man"].get<bool>();
	std::cout << sex << std::endl;

	if (j2.contains("behavior"))
	{
		json behaviorObj = j2["behavior"];
		auto funny = behaviorObj["funny"].get<std::string>();
		std::cout << funny << std::endl;
		auto worry = behaviorObj["worry"].get<std::string>();
		std::cout << worry << std::endl;
	}
	auto child1 = j2["child"][0].get<std::string>();
	auto child2 = j2["child"][1].get<std::string>();
	std::cout << child1 << typeid(child1).name() << " " << child2 << typeid(child2).name() <<std::endl;
	std::cout << "lisi" << std::endl;

	//构建animalArray
	json animalArray = { "cat","dog" };
	animalArray.push_back("pig");
	animalArray.emplace_back("duck");
	std::cout << animalArray << animalArray[animalArray.size() - 1].get<std::string>()<<std::endl;

	//构建animalObj
	json animalObj = { {"kind","dog"},{"height",50} };
	animalObj.push_back({ "color","red" });
	animalObj.erase("kind");
	animalObj["height"] = 99;
	//判断是否存在key三种方法
	if (animalObj.contains("height")&& animalObj.count("height")>0 && animalObj.find("height") != animalObj.end())
	{
		animalObj["weight"] = 66;//map实现 不存在的key则插入
		std::cout << animalObj << animalObj["height"].get<int>() << std::endl;
	}
	//遍历
	for (auto iter = animalObj.begin(); iter != animalObj.end(); ++iter)
	{
		std::cout << iter.key() << " " <<iter.value() << std::endl;
	}

	//序列化(dump)与反序列化(_json parse)

	std::string strhututu = R"({"name":"hututu","age":18,"score":88.99})";
	json hututuObj = R"({"name":"hututu","age":18,"score":88.99})"_json;
	json hututuObj2 = json::parse(strhututu);
	std::cout << hututuObj << std::endl;
	std::cout << hututuObj2 << std::endl;

	std::string strhututu1 = hututuObj2.dump();
	std::string strhututu2 = hututuObj2.dump(4);//加数字为分行显示同时每行前面加4个空格
	std::cout << strhututu1 << std::endl;
	std::cout << strhututu2 << std::endl;
}
```

# 一、稳定性和兼容性





## 1、原始字面量

​		C语言中表示字符的ASCII（American Standard Code for Interformation Interchange）编码中，字符可以实体表示也可以用字符的编码值进行转义表示，利用编码值间接表示的方式即为转义字符。

​        转义字符以`\`或者`\x`开头，以`\`开头表示后跟八进制形式的编码值，以`\x`开头表示后跟十六进制形式的编码值。对于转义字符来说，只能使用八进制或者十六进制。

​	对于 ASCII 编码，0~31（十进制）范围内的字符为控制字符，看不见，不能显示，只能用转义字符的形式来表示，但是ASCII码不好记，因此针对常用的控制字符转义简写表示。

![image-20220723000340632](thread.assets/image-20220723000340632.png)

​    

```c++
void CplussVersion11:: TestCharacter()
{
	char ch = 'a';
	std::cout << ch << " "<<(int)ch<< std::endl;
	char ch2;
	ch2 = 97;//ASCII码赋值
	std::cout << ch2 << " " << (int)ch2 << std::endl;

	std::cout << '\141' << std::endl;//转义字符8进制表示a的ASCII编码97
	std::cout << '\x61' << std::endl;//转义字符16进制表示a的ASCII编码97

	std::cout << "\x61\x62\x63" << std::endl;//转义字符组成字符串

	std::cout << '\\' << std::endl;//转义反斜杠
	std::cout << '\"' << std::endl;//转义双引号
}
```

   

 在C++中有时候初始化字符串，字符串中若存在反斜杠，引号等会造成初始化不准确，在 C++11 中添加了定义原始字符串的字面量，定义方式为：R “xxx(原始字符串)xxx” 其中（）两边的字符串可以省略。原始字面量 R 可以直接表示字符串的实际含义，而不需要**额外对字符串做转义或连接**等操作。

```C++
void CplussVersion11::Test_RXXX()
{
	//原始字面量 R “xxx (raw string) xxx”，（）两边的字符串在解析的时候是会被忽略的
	//因此一般不用指定。如果在（）前后指定了字符串，那么前后的字符串必须相同，否则会出现语法错误
	std::string str = "D:\hello\world\test.text";// \后无法构成转义的会忽略\ 
	std::cout << str << std::endl;
	std::string str1 = "D:\\hello\\world\\test.text";
	std::cout << str1 << std::endl;
	std::string str2 = R"(D:\hello\world\test.text)";//避免转义\ 跨行等R"(STR)"
	std::string str3 = R"test(\n\r\t\m\s\o\p\)test";
	std::cout << str2 << std::endl << str3 << std::endl;
}
```



![image-20220724182135388](thread.assets/image-20220724182135388.png)

##   2、long long型

```C++
   // C++11 标准要求 long long 整型可以在不同平台上有不同的长度，但至少有64位。long long 整型有两种表示
     //long long 与long long int  signed long long   signed long long int等价
    long long num1 = 123456789LL;//longlong型数据可以用LL或者ll进行后缀表示
    long long num2 = 123456789ll;
    //unsigned long long - 对应类型的数值可以使用 ULL(大写) 或者 ull(小写) 或者 Ull、uLL(等大小写混合)后缀
    //等价于unsigned long long int
    unsigned long long num3 = 123456781234ull;
    std::cout << num1 << std::endl << num2 << std::endl << num3 << std::endl;

    //同其他的整型一样，要了解平台上 long long 大小的方法就是查看 <climits>（或 <limits. h> ）中的宏与 long long 整 型相关的一共有 3 个：
        /*
        LLONG_MIN - 最小的 long long 值
        LLONG_MAX - 最大的 long long 值
        ULLONG MAX - 最大的 unsigned long long 值
        */
    std::cout << LLONG_MAX << std::endl << LLONG_MIN << std::endl << ULLONG_MAX << std::endl;
    // C++11 中一共只定义了以下 5 种标准的有符号整型：同时每一种整形对应有相同长度的无符号整型

      /* 
        signed char   
        short int
        int
        long int
        long long int
        */
    std::cout << "SCHAR_MAX"<<SCHAR_MAX << " "<<"SCHAR_MIN"<<SCHAR_MIN<<  std::endl;
    std::cout << "UCHAR_MAX" << UCHAR_MAX <<std::endl;
    std::cout << "LONG_MAX" << LONG_MAX << " " << "LONG_MIN" << LONG_MIN << std::endl;
    /*
    如果参与运算的数据或者传递的参数类型不匹配，整型间会发生隐式的转换，这种过程通常被称为整型的提升。
    比如如下表达式∶(int)num1 + (long long)num2
    长度越大的整型等级越高，比如 long long int 的等级会高于 int。
    长度相同的情况下，标准整型的等级高于扩展类型，比如 long long int 和 int64 如果都是 64 位长度，则 long long int 类型的等级更高。
    相同大小的有符号类型和无符号类型的等级相同，long long int 和 unsigned longlong int 的等级就相同。
    转换过程中，低等级整型需要转换为高等级整型，有符号的需要转换为无符号整形
    */
```

![1649815097986](thread.assets/1649815097986.png)

## 3，类初始化新特性

```c++
class TestInitClass
{
public:
	//在进行类成员变量初始化的时候，C++11 标准对于 C++98 做了补充，允许在定义类的时候在类内部直接对非静态成员变量进行初始化，
	//在初始化的时候可以使用等号 = 也可以 使用花括号 {} 且花括号 {} 的方式对类的非静态成员进行初始化，等号是可以省略不写的
	TestInitClass(int x, int y, int z) :a(x), b(y), c(z)//初始化列表 在就地初始化后在作用
	{}
	//在类内部对非静态成员变量就地初始化（C++11 新特性）
	int a = 9;
	int b = { 5 };
	int c{ 12 };
	double array1[4] = { 4.8, 4.6, 4.4, 4.2 };
	int array2[4]{ 3, 4, 5, 6 };//不加=
	char array3[4]{ 'a','b','c','d' };
	//静态成员
	static const int d;//const static属性无所谓前后顺序
	const static double e;
	static std::string f;
	//  string s1("hello");      error
	std::string s2{ "hello, world" };
	std::string s3 = "hello world";
	std::string s4 = { "hello world" };
};
//静态成员在类的外部初始化不用写static
const int TestInitClass::d = 10;
const double TestInitClass::e = 3.14;
std::string  TestInitClass::f = "Cplusplus";

void CplussVersion11::test_classInit()
{
	TestInitClass t(1, 2, 3);
	std::cout << t.a << " " << t.b << " " << t.c << " " << t.c << std::endl;
	std::cout<<t.s2<<" " << t.s3<<" "<<t.s4 << std::endl;
	for (int i = 0; i < 4; ++i)
	{
		std::cout << t.array1[i] << " " << t.array2[i] << " " << t.array3[i] << std::endl;

	}
	std::cout << TestInitClass::d << " " << t.e << " " << TestInitClass::f << std::endl;
}
```

![image-20220724192803318](thread.assets/image-20220724192803318.png)

## 4,final和override

```c++
//如果使用 final 修饰函数，只能修饰虚函数，这样就能阻止子类重写父类的这个函数了：
class Base
{
public:
    virtual void test()
    {
        cout << "Base class...";
    }
};

class Child : public Base
{
public:
    void test() final
    {
        cout << "Child class...";
    }
};

class GrandChild : public Child
{
public:
    // 语法错误, 不允许重写
    void test()
    {
        cout << "GrandChild class...";
    }
};

//使用 final 关键字修饰过的类是不允许被继承的，也就是说这个类不能有派生类。
C++

class Base
{
public:
    virtual void test()
    {
        cout << "Base class...";
    }
};

class Child final: public Base
{
public:
    void test()
    {
        cout << "Child class...";
    }
};

// error, 语法错误
class GrandChild : public Child
{
public:
};

//override 关键字确保在派生类中声明的重写函数与基类的虚函数有相同的签名，同时也明确表明将会重写基类的虚函数，这样就可以保证重写的虚函数的正确性，也提高了代码的可读性，和 final 一样这个关键字要写到方法的后面。使用方法如下：
class Base
{
public:
    virtual void test()
    {
        cout << "Base class...";
    }
};

//定了要重写父类的 test() 方法，使用了 override 关键字之后，假设在重写过程中因为误操作，写错了函数名或者函数参数或者返回值编译器都会提示语法错误，提高了程序的正确性，降低了出错的概率。
class Child : public Base
{
public:
    void test() override
    {
        cout << "Child class...";
    }
};

class GrandChild : public Child
{
public:
    void test() override
    {
        cout << "Child class...";
    }
};

```

## 5，模板优化

```C++
//-----------------------c++11支持>>连在一起写
#include<vector>
template <typename T = std::vector<int> >
class Base
{
public:
    void traversal(T& t)
    {
        auto it = t.begin();
        for (; it != t.end(); ++it)
        {
            std::cout << *it << " ";
        }
        std::cout << std::endl;
    }
};


void Test_Base()
{
   std:: vector<int> v{ 1,2,3,4,5,6,7,8,9 };
    Base<std::vector<int>> b;//C++98/03编译不通过 连续的>>会解析为位操作 需要空格 但是C++11要求编译器优先解析为模板
    Base<>c;
    b.traversal(v);
    c.traversal(v);
}

//------------------------------模板可以有默认的模板参数（C++98）
template <typename T = int, T t = 520>
class Test_template
{
public:
    void print()
    {
        std::cout << "current value: " << t << std::endl;
    }
};


void Template_Fun()
{
    //当所有模板参数都有默认参数时，函数模板的调用如同一个普通函数。但对于类模板而言，哪怕所有参数都有默认参数，在使用时也必须在模板名后跟随 <> 来实例化。
    Test_template<>t;
    t.print();
    Test_template<int, 1024> t1;
    t1.print();
}


// --------------函数咳哟有默认的模板参数，C++98/03不支持这种写法, C++11中支持这种写法
template <typename T = int>	
void func_template(T t)
{
   std:: cout << "current value: " << t << std::endl;
}

```

![1649821048323](thread.assets/1649821048323.png)

## 6，数值类型和字符串转换



```C++
//使用 to_string() 方法可以非常方便地将各种数值类型转换为字符串类型，这是一个重载函，函数声明位于头文件 <string> 中，函数原型如下：
string to_string (int val);
string to_string (long val);
string to_string (long long val);
string to_string (unsigned val);
string to_string (unsigned long val);
string to_string (unsigned long long val);
string to_string (float val);
string to_string (double val);
string to_string (long double val);


//由于 C++ 中的数值类型包括整形和浮点型，因此针对于不同的类型提供了不同的函数，通过调用这些函数可以将字符串类型转换为对应的数值类型。

// 定义于头文件 <string>
int       stoi( const std::string& str, std::size_t* pos = 0, int base = 10 );
long      stol( const std::string& str, std::size_t* pos = 0, int base = 10 );
long long stoll( const std::string& str, std::size_t* pos = 0, int base = 10 );

unsigned long      stoul( const std::string& str, std::size_t* pos = 0, int base = 10 );
unsigned long long stoull( const std::string& str, std::size_t* pos = 0, int base = 10 );

float       stof( const std::string& str, std::size_t* pos = 0 );
double      stod( const std::string& str, std::size_t* pos = 0 );
long double stold( const std::string& str, std::size_t* pos = 0 );

/*
str：要转换的字符串
pos：传出参数，记录从哪个字符开始无法继续进行解析，比如: 123abc, 传出的位置为 3
base：若 base 为 0 ，则自动检测数值进制：若前缀为 0 ，则为八进制，若前缀为 0x 或 0X，则为十六进制，否则为十进制。
*/

void CplussVersion11::str_value() {
	using namespace std;
	string pi = "pi is " + to_string(3.1415926);
	string love = "love is " + to_string(5.20 + 13.14);
	cout << pi << endl;
	cout << love << endl;

	string str1 = "9527";
	string str2 = "3.14159";
	string str3 = "9527tangyin";
	string str4 = "tangyin9527";

	int myint1 = std::stoi(str1);
	float myfloat2 = std::stof(str2);
	int myint3 = std::stoi(str3);
	// 错误： 'std::invalid_argument'
	// int myint4 = std::stoi(str4);
	size_t pos = 0;
	string str5 = "ff";
	int myint5 = std::stoi(str5, &pos, 16);//可以指定解读的进制 int long默认10 
	string str6 = "77";
	int myint6 = std::stoi(str6, &pos, 8);//8进制

	string str7 = "0xff";
	int myint7 = stoi(str7, &pos, 0);//0不指定 自行推到 0x开头16 

	string str8 = "1111";
	int myint8 = stoi(str8, &pos, 2);

	string str9 = "11";
	int myint9 = stoi(str9, &pos, 4);//4进制转换

	cout << "std::stoi(\"" << str1 << "\") is " << myint1 << endl;
	cout << "std::stof(\"" << str2 << "\") is " << myfloat2 << endl;
	cout << "std::stoi(\"" << str3 << "\") is " << myint3 << endl;
	cout << "std::stoi(\"" << str5 << "\") is " << myint5 << endl;
	cout << "std::stoi(\"" << str6 << "\") is " << myint6 << endl;
	cout << "std::stoi(\"" << str7 << "\") is " << myint7 << endl;
	cout << "std::stoi(\"" << str8 << "\") is " << myint8 << endl;
	cout << "std::stoi(\"" << str9 << "\") is " << myint9 << endl;
}

```

![image-20220727002206603](thread.assets/image-20220727002206603.png)

## 7，静态断言（static_assert）

```C++
/断言（assertion）是一种编程中常用的手段。在通常情况下，
//断言就是将一个返回值总是需要为真的判断表达式放在语句中，用于排除在设计的逻辑上不应该产生的情况
//若返回为假则会程序中断
#include<cassert>
void assert_test(int n)
{
    assert(n < 0);//单个参数 bool类型
    std::cout << n << std::endl;//运行时检查，若为假则程序中断
}
assert_test(0);
```

![1649860135350](thread.assets/1649860135350.png)

**静态断言 static_assert**，所谓静态就是在**编译时就能够进行检查的断言**，使用时**不需要引用头文件**。静态断言的另一个好处是，可以自定义违反断言时的错误提示信息。静态断言使用起来非常简单，它接收两个参数：

参数1：断言表达式，这个表达式通常需要返回一个 bool值
参数2：警告信息，它通常就是一段字符串，在违反断言（表达式为false）时提示该信息



由于静态断言的表达式是在编译阶段进行检测，所以在它的表达式中不能出现变量，也就是说这个表达式必须是常量表达式

```C++
void static_assert_test()
{
    using namespace std;
    // 字体原因看起来是一个=, 其实这是两个=
    static_assert(sizeof(long) == 8, "错误, 不是64位平台...");
    cout << "64bit Linux 指针大小: " << sizeof(char*) << endl;
    cout << "64bit Linux long 大小: " << sizeof(long) << endl;
}

```

![1649860491510](thread.assets/1649860491510.png)

![1649860697701](thread.assets/1649860697701.png)

## 8，异常

异常通常**用于处理逻辑上可能发生的错误**，在 C++98 中为我们提供了一套完善的异常处理机制，我们可以直接在程序中将各种类型的异常抛出，从而强制终止程序的运行。

![1649941350551](thread.assets/1649941350551.png)

**抛出异常会栈解旋：**

![1649941437251](thread.assets/1649941437251.png)

```C++
//声明可能抛出的异常类型 throw(type1,type2) 很少使用逐渐被C++11抛弃
//若不加则可以抛出任意类型异常  
//throw()  则表示不抛出异常 若抛出异常则会VS给出警告 warning C4297: “divisionMethod”: 假定函数不引发异常，但确实发生了 G++终止程序
//C++11 引入noexcept
/*
* noexcept 形如其名， 表示其修饰的函数不会抛出异常 。不过与 `throw ()` 动态异常声明不同的是，
`在 C++11 中如果 noexcept 修饰的函数抛出了异常，编译器可以选择直接调用 std::terminate () 函数来终止程序的运行，
这比基于异常机制的 throw () 在效率上会高一些`。这是因为异常机制会带来一些额外开销，比如函数抛出异常，
会导致函数栈被依次地展开（栈解旋），并自动调用析构函数释放栈上的所有对象。

double divisionMethod(int a, int b) noexcept(常量表达式);
常量表达式为值为true/false 
fun()noexcept（true）表示不抛出异常，若有异常则运行终止
fun()noexcept(false) 表示可能有异常
fun()noexcept 默认true表示不抛出异常，若有异常则运行终止
*/
double divisionMethod(int a, int b) noexcept(false)//throw()//throw(MyException, int)
{
    if (b == 0)
    {
        throw MyException("division by zero!!!");
        // throw 100;
    }
    return a/b;
}

void test_divisionMethod()
{
    using namespace std;
    try
    {
        double v = divisionMethod(100, 0);
        cout << "value: " << v << endl;
    }
    catch (int e)
    {
        cout << "catch except: " << e << endl;
    }
    catch (MyException e)
    {
        cout << "catch except: " << e.msg << endl;
    }
}
```

# 二、易学和易用性

## 1、自动类型推导

### auto



C++11 中 auto 并不代表一种实际的数据类型，**只是一个类型声明的 “占位符”，**auto 并不是万能的在任意场景下都能够推导出变量的实际类型，使用auto声明的变量**必须要进行初始化，以让编译器推导出它的实际类型，**在编译时将auto占位符替换为真正的类型。

```C++
auto x = 3.14;      // x 是浮点型 double
auto y = 520;       // y 是整形 int
auto z = 'a';       // z 是字符型 char
auto nb;            // error，变量必须要初始化
auto double nbl;    // 语法错误, 不能修改数据类型  

//当变量不是指针或者引用类型时，推导的结果中不会保留 const、volatile 关键字
//当变量是指针或者引用类型时，推导的结果中会保留 const、volatile 关键字
int temp = 110;
auto *a = &temp;	
auto b = &temp;		
auto &c = temp;		
auto d = temp;		
/*
变量 a 的数据类型为 int*，因此 auto 关键字被推导为 int类型
变量 b 的数据类型为 int*，因此 auto 关键字被推导为 int* 类型
变量 c 的数据类型为 int&，因此 auto 关键字被推导为 int类型
变量 d 的数据类型为 int，因此 auto 关键字被推导为 int 类型
在来看一组带 const 限定的变量，使用 auto 进行类型推导的例子:
*/


int tmp = 250;
const auto a1 = tmp;
auto a2 = a1;
const auto &a3 = tmp;
auto &a4 = a3;
/*
变量 a1 的数据类型为 const int，因此 auto 关键字被推导为 int 类型
变量 a2 的数据类型为 int，但是 a2 没有声明为指针或引用因此 const 属性被去掉，auto 被推导为 int
变量 a3 的数据类型为 const int&，a3 被声明为引用因此 const 属性被保留，auto 关键字被推导为 int 类型
变量 a4 的数据类型为 const int&，a4 被声明为引用因此 const 属性被保留，auto 关键字被推导为 const int 类型
*/

//几种应用场景

//遍历容器
int main()
{
    map<int, string> person;
    // 代码简化
    for (auto it = person.begin(); it != person.end(); ++it)
    {
        // do something
    }
    return 0;
}
//用于泛型编程，在使用模板的时候，很多情况下我们不知道变量应该定义为什么类型

class T1
{
public:
    static int get()
    {
        return 10;
    }
};

class T2
{
public:
    static string get()
    {
        return "hello, world";
    }
};

template <class A>
void func(void)
{
    auto val = A::get();//不知道A的返回类型
    cout << "val: " << val << endl;
}

int main()
{
    func<T1>();
    func<T2>();
    return 0;
}
//若不用auto 则要多定义一个泛型参数且调用时进行强制指定
template <class A, typename B>        // 添加了模板参数 B
void func(void)
{
    B val = A::get();
    cout << "val: " << val << endl;
}

int main()
{
    func<T1, int>();                  // 手动指定返回值类型 -> int
    func<T2, string>();               // 手动指定返回值类型 -> string
    return 0;
}


```




### decltype

C++11 提供的 decltype 关键字了，它的作用是在编译器编译的时候推导出一个表达式的类型,decltype 是 “declare type” 的缩写，意思是 “声明类型”。decltype 的推导是在**编译期完成的**，它只是用于**表达式类型的推导**，并不会计算表达式的值。

```c++
int a = 10;
decltype(a) b = 99;                 // b -> int
decltype(a+3.14) c = 52.13;         // c -> double
decltype(a+b*c) d = 520.1314;       // d -> double

```

关于 decltype 的应用多出现在泛型编程中。比如我们编写一个类模板，在里边添加遍历容器的函数。

```C++
template <class T>
class Container
{
public:
    void func(T& c)
    {
        for (m_it = c.begin(); m_it != c.end(); ++m_it)
        {
            cout << *m_it << " ";
        }
        cout << endl;
    }
private:
    decltype(T().begin()) m_it;  // 这里不能确定迭代器类型 匿名对象begin推到
};

int main()
{
    const list<int> lst{ 1,2,3,4,5,6,7,8,9 };
    Container<const list<int>> obj;
    obj.func(lst);
    return 0;
}


```

**返回值类型后置：**

```C++
//在C++11中增加了返回类型后置语法，说明白一点就是将decltype和auto结合起来完成返回类型的推导。其语法格式如下:
// 符号 -> 后边跟随的是函数返回值的类型
//auto func(参数1, 参数2, ...) -> decltype(参数表达式)

template <typename T, typename U>
// 返回类型后置语法
auto add(T t, U u) -> decltype(t+u) //在定义模板函数的时候可以用此推到返回值类型
{
    return t + u;
}

int main()
{
    int x = 520;
    double y = 13.14;
    // auto z = add<int, double>(x, y);
    auto z = add(x, y);		// 简化之后的写法
    cout << "z: " << z << endl;
    return 0;
}



```

## 2，for范围语句

```C++
void test_for()
{
    using namespace std;

    vector<int> t{ 1,2,3,4,5,6 };
    //使用迭代器 auto在普通的for循环中推到出迭代器类型（指针类似）
    for (auto it = t.begin(); it != t.end(); ++it)
    {
        cout << *it << " ";
    }
    cout << endl;
    //基于范围的for循环中，不需要再传递容器的两端，循环会自动以容器为范围展开，并且循环中
    // 也屏蔽掉了迭代器的遍历细节，直接抽取容器中的元素进行运算，使用这种方式进行循环遍历会让编码和维护变得更加简便。

    /*
    语法格式中 declaration 表示遍历声明，在遍历过程中，当前被遍历到的元素会被存储到声明的变量中。
    expression 是要遍历的对象，它可以是表达式、容器、数组、初始化列表等。
     for (declaration : expression)
    {
         // 循环体

    }
    */
    // 将容器中遍历的当前元素拷贝到了声明的变量 value 中,因此无法改变，只读
    for (auto value : t)
    {
        cout << value << " ";
    }
    cout << endl;

    //若要改变则需要使用引用auto&
    for (auto&value : t)
    {
        value++;
    }
    //对容器的遍历过程中，如果只是读数据，不允许修改元素的值，可以使用 const 定义保存元素数据的变量
    //在定义的时候建议使用 const auto &，这样相对于 const auto 效率要更高一些(避免拷贝)
    for (const auto& value : t)
    {
        cout << value << " ";
    }
    cout << endl;

    //关系型容器中的使用细节
    map<int, string> m{
     {1, "lucy"},{2, "lily"},{3, "tom"}
    };

    // 基于范围的for循环方式
    for (auto& it : m)
    {
        cout << "id: " << it.first << ", name: " << it.second << endl;
    }

    // 普通的for循环方式
    for (auto it = m.begin(); it != m.end(); ++it)
    {
        cout << "id: " << it->first << ", name: " << it->second << endl;
    }
    
    /*
    使用普通的 for 循环方式（基于迭代器）遍历关联性容器， auto 自动推导出的是一个迭代器类型，需要使用迭代器的方式取出元素中的键值对（和指针的操作方法相同）：
    it->first
    it->second
    使用基于访问的 for 循环遍历关联性容器，auto 自动推导出的类型是容器中的 value_type，相当于一个对组（std::pair）对象，提取键值对的方式如下：
    it.first
    it.second
    */

    //set map关系型 不可以改变key （红黑树有序会调整）
    //因此在 for 循环中 auto & 会被视为 const auto & 
    //在遍历关联型容器时也会出现同样的问题，基于范围的for循环中，虽然可以得到一个std::pair引用，
    //但是我们是不能修改里边的first值的，也就是key值。
    set<int>s = { 1,2,5,6,3,9,0 };
    for (auto& value : s)//等价于const auto&
    {
        cout << value << " ";
    }
    cout  << endl;

    for (auto& value : m)//map不可以改first但是可以改second不影响树的平衡性
    {
        value.second = "lili" + to_string(value.first);
    }
    for (auto& value : m)//等价于const auto&
    {
        cout << value .first<< " "<<value.second;
    }
    cout << endl;
}

```

![1650290401901](thread.assets/1650290401901.png)

## 3，nullptr



![1650291536439](thread.assets/1650291536439.png)

```C++
void func(char *p)
{
    cout << "void func(char *p)" << endl;
}

void func(int p)
{
    cout << "void func(int p)" << endl;
}

int main()
{
    func(NULL);   // 想要调用重载函数 void func(char *p)  重载为int 歧义
    func(250);    // 想要调用重载函数 void func(int p)

    return 0;
}

```

## 4，lambda表达式

lambda 表达式是 C++11 最重要也是最常用的特性之一，这是现代编程语言的一个特点，lambda 表达式有如下的一些优点：

**声明式的编程风格：**就地匿名定义目标函数或函数对象，不需要额外写一个命名函数或函数对象。
**简洁：**避免了代码膨胀和功能分散，让开发更加高效。
在需要的时间和地点实现功能闭包，使程序更加**灵活**

lambda 表达式定义了一个匿名函数，并且可以捕获一定范围内的变量。

```C++
[capture](params) opt -> ret {body;};


```

**其中 capture 是捕获列表**，**params 是参数列表**，**opt 是函数选项**，**ret 是返回值类型**，**body 是函数体**。
捕获列表 []: 捕获一定范围内的变量

**参数列表 ():** 和普通函数的参数列表一样，**如果没有参数参数列表可以省略不写。**

auto f = [](){return 1;}	// 没有参数, 参数列表为空
auto f = []{return 1;}		// 没有参数, 参数列表省略不写
**opt 选项， 不需要可以省略**

​       1,**mutable:** 可以**修改按值传递进来的拷贝**（注意是能修改拷贝，而不是值本身）
​       2,**exception:** 指定函数抛出的异常，如抛出整数类型的异常，可以使用 throw ();
**返回值类型**：在 C++11 中，lambda 表达式的返回值是通过返回值**后置语法来定义的**。

函数体：函数的实现，这部分不能省略，但函数体可以为空。



**关于捕获列表**



![1650465279670](../../Qt学习/Cpluss11Traicts/Cplusplus11.assets/1650465279670.png)



```C++
class Test
{
public:
    void output(int x, int y)
    {
        auto x1 = [] {return m_number; };                 // error  没有捕获外部变量不能用m_number
        auto x2 = [=] {return m_number + x + y; };        // ok 值传递捕获所有可用成员和外部可见参
        auto x3 = [&] {return m_number + x + y; };        // ok 引用传递
        auto x4 = [this] {return m_number; };                  // ok  捕获this可访问内部成员
        auto x5 = [this] {return m_number + x + y; };          // error 只是捕获this无法访问x,y
        auto x6 = [this, x, y] {return m_number + x + y; };    // ok 捕获this和x,y
        auto x7 = [this] {return m_number++; };                // ok  捕获this且修改内部成员
        
        int a = 0;
        auto f2 = [&] {return a++; };                     // ok  引用可读写
        auto f3 = [=] {return a; };                       // ok  
        auto f4 = [=] {return a++; };                     // error 值拷贝只读不可修改
/*
在匿名函数内部，需要通过 lambda 表达式的捕获列表控制如何捕获外部变量，以及访问哪些变量。默认状态下 lambda 表达式无法修改通过复制方式捕获外部变量，如果希望修改这些外部变量，需要通过引用的方式进行捕获。
*/
    }
    int m_number = 100;
};


```

**返回值**

很多时候，lambda 表达式的返回值是非常明显的，因此在 C++11 中允许省略 lambda 表达式的返回值。

![1650465890837](thread.assets/1650465890837.png)

**函数本质**

​	使用 lambda 表达式捕获列表捕获外部变量，**如果希望去修改按值捕获的外部变量**，这就需要使用 mutable 选项，被mutable修改是lambda表达式就算没有参数也要写明参数列表，并且可以去掉按值捕获的外部变量的只读（const）属性。

```C++
int a = 0;
auto f1 = [=] {return a++; };              // error, 按值捕获外部变量, a是只读的
auto f2 = [=]()mutable {return a++; };     // ok


```


***为什么通过值拷贝的方式捕获的外部变量是只读的:***

​	lambda表达式的类型在C++11中会被看做是一个**带operator()的类，即仿函数**。
​	按照C++标准，lambda表达式的operator()默认是const的，**一个const成员函数是无法修改成员变量值的**。
mutable 选项的作用就在于取消 operator () 的 const 属性。

因为 lambda 表达式在 C++ 中会被看做是一个仿函数，因此可以使用**std::function和std::bind来存储和操作lambda表达式：**

```c++
int main(void)
{
    // 包装可调用函数
    std::function<int(int)> f1 = [](int a) {return a; };
    // 绑定可调用函数
    std::function<int(int)> f2 = bind([](int a) {return a; }, placeholders::_1);

    // 函数调用
    cout << f1(100) << endl;
    cout << f2(200) << endl;
    return 0;
}

```



#  三、多线程

## 1,日期和时间相关的库 chrono

 chrono 库主要包含三种类型的类：时间间隔duration、时钟clocks、时间点time point。

### 1.1 duration

duration表示一段时间间隔，用来记录时间长度，可以表示几秒、几分钟、几个小时的时间间隔

```C++
// 定义于头文件 <chrono>
template<
    class Rep,
    class Period = std::ratio<1>
> class duration;

//Rep：这是一个数值类型，表示时钟数（周期）的类型（默认为整形）。若 Rep 是浮点数，则 duration 能使用小数描述时钟周期的数目
//Period：表示时钟的周期，它的原型如下
// 定义于头文件 <ratio>
template<
    std::intmax_t Num,//周期的分子
    std::intmax_t Denom = 1//周期的分母 默认为1
> class ratio;

//ratio 类表示每个时钟周期的秒数，其中第一个模板参数 Num代表分子，Denom代表分母，该分母值默认为 1，因此，ratio代表的是一个分子除以分母的数值，比如：ratio<2> 代表一个时钟周期是 2 秒，ratio<60 > 代表一分钟，ratio<60*60 > 代表一个小时，ratio<60*60*24 > 代表一天。而 ratio<1,1000 > 代表的是 1/1000 秒，也就是 1 毫秒，ratio<1,1000000 > 代表一微秒，ratio<1,1000000000 > 代表一纳秒。



```

![1650980779062](thread.assets/1650980779062.png)

```C++
void test_chrono()
{
    using namespace std;
    chrono::hours h(1);                          // 一小时
    chrono::milliseconds ms{ 3 };                // 3 毫秒  初始化操作 ms{3} 表示一共有 3 个时间周期，每个周期为 1 毫秒
    std::chrono::microseconds us = 2 * ms;     // 6000 微秒
    chrono::duration<int, ratio<1000>> ks(3);    // 3000 秒

    // chrono::duration<int, ratio<1000>> d3(3.5);  // error
    //dd(6.6) 时钟周期为默认的 1 秒，共有 6.6 个时钟周期，所以 dd 表示的时间间隔为 6.6 秒
    chrono::duration<double> dd(6.6);               // 6.6 秒 周期类型为小数

    // 使用小数表示时钟周期的次数
    //hz(3.5) 时钟周期为 1 / 30 秒，共有 3.5 个时钟周期，所以 hz 表示的时间间隔为 1 / 30 * 3.5 秒
    chrono::duration<double, std::ratio<1, 30>> hz(3.5);
    //count统计周期数
    std::cout << "3 ms duration has " << ms.count() << " ticks\n"  //3
        << "6000 us duration has " << us.count() << " ticks\n"     //6000
        << "3.5 hz duration has " << hz.count() << " ticks\n";     //3.5

    //重载了++ -- + - = 等操作
    chrono::minutes t1(2);
    chrono::seconds t2(2);
    chrono::seconds t3 = t1 - t2;
    chrono::seconds t4 = t1 + t2;
    t4++;
    cout << t3.count() <<"seconds" << endl;//118seconds
    cout << t4.count() << "seconds" << endl;//123seconds
    /*
    注意事项：duration 的加减运算有一定的规则，当两个 duration 时钟周期不相同的时候，会先统一成一种时钟，然后再进行算术运算，统一的规则如下：假设有 ratio<x1,y1> 和 ratio<x2,y2 > 两个时钟周期，首先需要求出 x1，x2 的最大公约数 X，然后求出 y1，y2 的最小公倍数 Y，统一之后的时钟周期 ratio 为 ratio<X,Y>
    */
    
    chrono::duration<double, ratio<9, 7> >t5(3);//3*   9/7秒
    chrono::duration<double, ratio<4, 3>> t6(3); // 3 *   4/3秒
    chrono::duration<double, ratio<1, 21>> t7 = t6 - t5;
    cout << t7.count() << "ticks" << endl;//3ticks

}

```

### 1.2 时间点time_point

```C++
// 定义于头文件 <chrono>
template<
    class Clock,
    class Duration = typename Clock::duration
> class time_point;
//它被实现成如同存储一个 Duration 类型的自 Clock 的纪元起始开始的时间间隔的值，通过这个类最终可以得到时间中的某一个时间点


// 1. 构造一个对象，表示一个时间点，其中d的持续时间从epoch开始，需要和时钟类一起使用，不能单独使用该构造函数
explicit time_point( const duration& d );
// 2. 拷贝构造函数，构造与t相同时间点的对象，使用的时候需要指定模板参数
template< class Duration2 >
time_point( const time_point<Clock,Duration2>& t );

//在time_point类中除了构造函数还提供了另外一个 time_since_epoch() 函数，用来获得 1970 年 1 月 1 日到 time_point 对象中记录的时间经过的时间间隔（duration），函数原型如下：

duration time_since_epoch() const;
//时间点 time_point 对象和时间段对象 duration 之间还支持直接进行算术运算（即加减运算），时间点对象之间可以进行逻辑运算

```

### 1.3 时钟clocks

chrono 库中提供了获取当前的系统时间的时钟类，包含的时钟一共有三种：

**system_clock：**系统的时钟，系统的时钟可以修改，甚至可以网络对时，因此使用系统时间计算时间差可能不准。
**steady_clock：**是固定的时钟，相当于秒表。开始计时后，时间只会增长并且不能修改，适合用于记录程序耗时。
**high_resolution_clock：**和时钟类 steady_clock 是等价的（是它的别名）。
在这些时钟类的内部有 time_point、duration、Rep、Period 等信息，基于这些信息来获取当前时间，以及实现 time_t 和 time_point 之间的相互转换。

```C++
#include<time.h>
void test_clocks()
{
    using namespace std;
    using namespace std::chrono;
    // 新纪元1970.1.1时间
    system_clock::time_point epoch;

    duration<int, ratio<60 * 60 * 24>> day(1);
    // 新纪元1970.1.1时间 + 1天
    system_clock::time_point ppt(day);

    using dday = duration<int, ratio<60 * 60 * 24>>;
    // 新纪元1970.1.1时间 + 10天
    time_point<system_clock, dday> t(dday(10));

    // 系统当前时间
    system_clock::time_point today = system_clock::now();

    // 转换为time_t时间类型
    time_t tm = system_clock::to_time_t(today);
    cout << "今天的日期是:    " << ctime(&tm);

    time_t tm1 = system_clock::to_time_t(today + day);
    cout << "明天的日期是:    " << ctime(&tm1);

    time_t tm2 = system_clock::to_time_t(epoch);
    cout << "新纪元时间:      " << ctime(&tm2);

    time_t tm3 = system_clock::to_time_t(ppt);
    cout << "新纪元时间+1天:  " << ctime(&tm3);

    time_t tm4 = system_clock::to_time_t(t);
    cout << "新纪元时间+10天: " << ctime(&tm4);
}

```


 **steady_clock**
如果我们通过时钟不是为了获取当前的系统时间，而是进行程序耗时的时长，此时使用 syetem_clock 就不合适了，因为这个时间可以跟随系统的设置发生变化。在 C++11 中提供的时钟类 steady_clock 相当于秒表，只要启动就会进行时间的累加，并且不能被修改，非常适合于进行耗时的统计。

```C++
	void test_stedy_time()
{
    using namespace chrono;
    // 获取开始时间点
    steady_clock::time_point start = steady_clock::now();
    // 执行业务流程
    cout << "print 1000 stars ...." << endl;
    for (int i = 0; i < 1000; ++i)
    {
        cout << "*";
    }
    cout << endl;
    // 获取结束时间点
    steady_clock::time_point last = steady_clock::now();
    // 计算差值
    auto dt = last - start;
    cout << "总共耗时: " << dt.count() << "纳秒" << endl;
}

```

![1651393999959](thread.assets/1651393999959.png)

## 2,thread

C++11 中提供的线程类叫做 std::thread，基于这个类创建一个新的线程非常的简单，只需要提供线程函数或者函数对象即可，并且可以同时指定线程函数的参数。

```C++
// ①
thread() noexcept;
// ②
thread( thread&& other ) noexcept;
//移动构造函数，将 other 的线程所有权转移给新的 thread 对象。之后 other 不再表示执行线程。
// ③
template< class Function, class... Args >
explicit thread( Function&& f, Args&&... args );
// ④
thread( const thread& ) = delete;
//使用 =delete 显示删除拷贝构造，不允许线程对象之间的拷贝

```

```C++
void test_thread()
{
    cout << "主线程的线程ID: " << this_thread::get_id() << endl;
    thread t1(t_fun1, 1314, "一生一世");
    thread t2(t_fun2);
    cout << "线程t 的线程ID: " << t1.get_id() << endl;
    cout << "线程t1的线程ID: " << t2.get_id() << endl;
    //线程回收 join 和 detach
    //join() 字面意思是连接一个线程，意味着主动地等待线程的终止（线程阻塞）,在某个线程中通过子线程对象调用 join() 函数;
    //调用这个函数的线程被阻塞，但是子线程对象中的任务函数会继续执行，当任务执行完毕之后 join() 会清理当前子线程中的相关资源然后返回，
    //同时，调用该函数的线程解除阻塞继续向下执行。


    this_thread::sleep_for(chrono::seconds(1));
    std::cout << "--------------" << t1.joinable() << " " << t2.joinable() << endl;
    //在创建的子线程对象的时候，如果指定了任务函数，子线程启动并执行任务，主线程和这个子线程自动连接成功


    t1.join();//当主线程运行到 t1.join();，根据子线程对象 t1 的任务函数 func() 的执行情况，主线程会做如下处理：
              //如果任务函数 func() 还没执行完毕，主线程阻塞，直到任务执行完毕，主线程解除阻塞，继续向下运行
              //如果任务函数 func() 已经执行完毕，主线程不会阻塞，继续向下运行.
    t2.detach();//detach() 函数的作用是进行线程分离，分离主线程和创建出的子线程。在线程分离之后，主线程退出也会一并销毁创建出的所有子线程，
             //在主线程退出之前，它可以脱离主线程继续独立的运行，任务执行完毕之后，这个子线程会自动释放自己占用的系统资源。
              //线程分离函数没有参数也没有返回值，只需要在线程成功之后，通过线程对象调用该函数即可
              
            
    //joinable() 函数用于判断主线程和子线程是否处理关联（连接）状态 返回bool
    std::cout << "--------------" << t1.joinable() << " " << t2.joinable() << endl;
    //子线程调用了detach()函数之后，父子线程分离，同时二者的连接断开，调用joinable()返回false
    // 在子线程调用了join()函数，子线程中的任务函数继续执行，直到任务处理完毕，这时join()会清理（回收）当前子线程的相关资源，
    //所以这个子线程和主线程的连接也就断开了，因此，调用join()之后再调用joinable()会返回false

    this_thread::sleep_for(chrono::seconds(6));//脱离线程后要保证在子线程销毁前主线程未将其对象消亡，否则无法；

    /*
    线程分离函数 detach () 不会阻塞线程，子线程和主线程分离之后，在主线程中就不能再对这个子线程做任何控制了，
    比如：通过 join () 阻塞主线程等待子线程中的任务执行完毕，或者调用 get_id () 获取子线程的线程 ID。
。*/

    /*
    thread 线程类还提供了一个静态方法，用于获取当前计算机的 CPU 核心数，根据这个结果在程序中创建出数量相等的线程，
    每个线程独自占有一个CPU核心，这些线程就不用分时复用CPU时间片，此时程序的并发效率是最高的。
    */
    int num = thread::hardware_concurrency();
    cout << "CPU number: " << num << endl;

}

```

![1651401660132](thread.assets/1651401660132.png)

## 3,this_thread**命名空间**

在这个命名空间中提供了四个公共的成员函数，通过这些成员函数就可以对当前线程进行相关的操作了。

**线程状和进程态一样：**



![1651400843547](thread.assets/1651400843547.png)

![1651401005555](thread.assets/1651401005555.png)

命名空间 this_thread 中提供了一个休眠函数 sleep_for()，调用这个函数的线程会马上**从运行态变成阻塞态并在这种状态下休眠一定的时长，因为阻塞态的线程已经让出了 CPU 资源，代码也不会被执行，所以线程休眠过程中对 CPU 来说没有任何负担**。这个函数是函数原型如下，参数需要指定一个休眠时长，是一个时间段：

```C++
void test_this_thread()
{
    std::cout<<this_thread::get_id()<<endl;//获取当前线程的id 
    this_thread::sleep_for(chrono::seconds(1));//休眠一秒 阻塞态 让出1秒时间片

    // void sleep_for (const chrono::duration<Rep,Period>& rel_time);
    //void sleep_until (const chrono::time_point<Clock,Duration>& abs_time);
    // 获取当前系统时间点
    auto now = chrono::system_clock::now();
    // 时间间隔为2s
    chrono::seconds sec(2);
    // 当前时间点之后休眠两秒 
    this_thread::sleep_until(now + sec);//休眠知道time_point之后
    
     for (int i = 0; i < 100000000000; ++i)
    {
        cout << "子线程: " << this_thread::get_id() << ", i = " << i << endl;
        this_thread::yield();//避免当前线程一直抢占时间片其他线程无法执行一直处于就绪态，执行一次让出本次时间片，但是同时会参与下一次时间片抢夺。
    }
    
    //一个非常绅士的函数 yield()，在线程中调用这个函数之后，处于运行态的线程会主动让出自己已经抢到的 CPU 时间片，最终变为就绪态，这样其它的线程就有更大的概率能够抢到 CPU 时间片了。使用这个函数的时候需要注意一点，线程调用了 yield () 之后会主动放弃 CPU 资源，但是这个变为就绪态的线程会马上参与到下一轮 CPU 的抢夺战中，不排除它能继续抢到 CPU 时间片的情况，这是概率问题。
}

```

## 4,call_once函数

​        在某些特定情况下，某些函数**只能在多线程环境下调用一次，比如：要初始化某个对象**，**而这个对象只能被初始化一次，**就可以使用 std::call_once() 来保证函数在多线程环境下只能被调用一次。使用 call_once() 的时候，需要一个 once_flag 作为 call_once() 的传入参数。

**<u>void call_once( std::once_flag& flag, Callable&& f, Args&&... args );</u>**

*flag：once_flag 类型的对象，要保证这个对象能够被多个线程同时访问到。*

*f：回调函数，可以传递一个有名函数地址，也可以指定一个匿名函数*

*args：作为实参传递给回调函数。*

```C++
#include <iostream>
#include <thread>
#include <mutex>
using namespace std;

once_flag g_flag;
void do_once(int a, string b)
{
    cout << "name: " << b << ", age: " << a << endl;
}

void do_something(int age, string name)
{
    static int num = 1;
    call_once(g_flag, do_once, 19, "luffy");
    cout << "do_something() function num = " << num++ << endl;
}

void test_call_once()
{
    thread t1(do_something, 20, "ace");
    thread t2(do_something, 20, "sabo");
    thread t3(do_something, 19, "luffy");
    t1.join();
    t2.join();
    t3.join();
}

```

![1651403768386](thread.assets/1651403768386.png)

## 5,线程同步之互斥锁

​	进行多线程编程，如果多个线程需要对同一块内存进行操作，比如：同时读、同时写、同时读写对于后两种情况来说，如果不做任何的人为干涉就会出现各种各样的错误数据。这是因为线程在运行的时候需要先得到 CPU 时间片，时间片用完之后需要放弃已获得的 CPU 资源，就这样线程频繁地在就绪态和运行态之间切换，更复杂一点还可以在就绪态、运行态、挂起态之间切换，这样就会导致线程的执行顺序并不是有序的，而是随机的混乱的。

​    **线程同步：**

假设有 4 个线程 A、B、C、D，当前一个线程 A 对内存中的共享资源进行访问的时候，其他线程 B, C, D 都不可以对这块内存进行操作，直到线程 A 对这块内存访问完毕为止，B，C，D 中的一个才能访问这块内存，剩余的两个需要继续阻塞等待，以此类推，直至所有的线程都对这块内存操作完毕。 线程对内存的这种访问方式就称之为线程同步，

​    解决多线程数据混乱的方案就是进行线程同步，最常用的就是互斥锁。

**在C++11 中提供了四种互斥锁：**

*std::mutex：*独占的互斥锁，不能递归使用
*std::timed_mutex：*带超时的独占互斥锁，不能递归使用
*std::recursive_mutex：*递归互斥锁，不带超时功能
*std::recursive_timed_mutex：*带超时的递归互斥锁
互斥锁在有些资料中也被称之为互斥量，二者是一个东西。

### 5.1 std::mutex

![1651405615090](thread.assets/1651405615090.png)

互斥锁基本思路：

- 找到多个线程操作的共享资源（全局变量、堆内存、类成员变量等），也可以称之为*临界资源*
- 找到和共享资源有关的上下文代码，也就是临界区（下图中的黄色代码部分）
- 在临界区的上边调用互斥锁类的 lock() 方法
- 在临界区的下边调用互斥锁的 unlock() 方法

![1651405414347](thread.assets/1651405414347.png)



```C++
    this_thread::sleep_for(chrono::seconds(1));
}

```

### 5.2 std::lock_guard

​    	lock_guard 是 C++11 新增的一个模板类，使用这个类，可以简化互斥锁 lock() 和 unlock() 的写法，同时也更安全。lock_guard 在使用时，**会自动锁定互斥量，而在退出作用域后进行析构时就会自动解锁**，从而保证了互斥量的正确操作，避免忘记 unlock() 操作而导致线程死锁。lock_guard 使用了 RAII 技术，就是在类构造函数中分配资源，在析构函数中释放资源，保证资源出了作用域就释放

```c++
void slow_increment(int id)
{
    for (int i = 0; i < 3; ++i) 
    {
        // 使用哨兵锁管理互斥锁
        lock_guard<mutex> lock(g_num_mutex);
        ++g_num;
        cout << id << " => " << g_num << endl;
        this_thread::sleep_for(chrono::seconds(1));
    }
}
//通过修改发现代码被精简了，而且不用担心因为忘记解锁而造成程序的死锁，但是这种方式也有弊端，在上面的示例程序中整个for循环的体都被当做了临界区，多个线程是线性的执行临界区代码的，因此临界区越大程序效率越低，还是需要根据实际情况选择最优的解决方案。

```

### 5.3 std::recursive_mutex

​	递归互斥锁 std::recursive_mutex **允许同一线程多次获得互斥锁**，可以用来解决同一线程需要多次获取互斥量时死锁的问题，在下面的例子中使用独占非递归互斥量会发生死锁。

```C++
#include <iostream>
#include <thread>
#include <mutex>
using namespace std;

struct Calculate
{
    Calculate() : m_i(6) {}

    void mul(int x)
    {
        lock_guard<recursive_mutex> locker(m_mutex);
        m_i *= x;
    }

    void div(int x)
    {
        lock_guard<recursive_mutex> locker(m_mutex);
        m_i /= x;
    }

    void both(int x, int y)
    {
        lock_guard<recursive_mutex> locker(m_mutex);
        mul(x);//both调用了递归锁 mul和div在调 递归锁在同一个线程中可多次授权 没问题
        div(y);
    }

    int m_i;
    recursive_mutex m_mutex;//
};

int main()
{
    Calculate cal;
    cal.both(6, 3);
    cout << "cal.m_i = " << cal.m_i << endl;
    return 0;
}


```

![1651406312602](thread.assets/1651406312602.png)

### 5.4  std::timed_mutex

​	std::timed_mutex 是**超时独占互斥锁**，主要是在获取互斥锁资源时**增加了超时等待功能**，因为不知道获取锁资源需要等待多长时间，为了保证不一直等待下去，设置了一个超时时长**，超时后线程就可以解除阻塞去做其他事情了。**

***std::timed_mutex 比 std::_mutex 多了两个成员函数：try_lock_for() 和 try_lock_until()：***


![1651406477720](thread.assets/1651406477720.png)

```C++
#include <iostream>
#include <thread>
#include <mutex>
using namespace std;

timed_mutex g_mutex;

void work()
{
    chrono::seconds timeout(1);
    while (true)
    {
        // 通过阻塞一定的时长来争取得到互斥锁所有权
        if (g_mutex.try_lock_for(timeout))
        {
            cout << "当前线程ID: " << this_thread::get_id() 
                << ", 得到互斥锁所有权..." << endl;
            // 模拟处理任务用了一定的时长
            this_thread::sleep_for(chrono::seconds(10));
            // 互斥锁解锁
            g_mutex.unlock();
            break;
        }
        else
        {
            cout << "当前线程ID: " << this_thread::get_id() 
                << ", 没有得到互斥锁所有权..." << endl;
            // 模拟处理其他任务用了一定的时长
            this_thread::sleep_for(chrono::milliseconds(50));
        }
    }
}

int main()
{
    thread t1(work);
    thread t2(work);
    t1.join();
    t2.join();

    return 0;
}

//在上面的例子中，通过一个 while 循环不停的去获取超时互斥锁的所有权，如果得不到就阻塞 1 秒钟，1 秒之后如果还是得不到阻塞 50 毫秒，然后再次继续尝试，直到获得互斥锁的所有权，跳出循环体。

//关于递归超时互斥锁 std::recursive_timed_mutex 的使用方式和 std::timed_mutex 是一样的，只不过它可以允许一个线程多次获得互斥锁所有权，而 std::timed_mutex 只允许线程获取一次互斥锁所有权。另外，递归超时互斥锁 std::recursive_timed_mutex 也拥有和 std::recursive_mutex 一样的弊端，不建议频繁使用。


```

## 6.线程同步之条件变量

![1651667996202](thread.assets/1651667996202.png)

![1651668133892](thread.assets/1651668133892.png)

```C++
#include <iostream>
#include <thread>
#include <mutex>
#include <list>
#include <functional>
#include <condition_variable>
using namespace std;

//condition_variable：需要配合 std::unique_lock<std::mutex> 进行 wait 操作，也就是阻塞线程的操作。
//主要两个成员函数 阻塞wait和唤醒notify
//void wait (unique_lock<mutex>& lck);  调用该函数的线程直接被阻塞
//void wait (unique_lock<mutex>& lck, Predicate pred);  
//函数的第二个参数是一个判断条件，是一个返回值为布尔类型的函数;表达式返回false当前线程被阻塞，表达式返回true当前线程不会被阻塞，继续向下执行
//独占的互斥锁对象不能直接传递给 wait() 函数，需要通过模板类 unique_lock 进行二次处理
//如果线程被该函数阻塞，这个线程会释放占有的互斥锁的所有权，当阻塞解除之后这个线程会重新得到互斥锁的所有权，继续向下执行

//notify_one()：唤醒一个被当前条件变量阻塞的线程
//notify_all()：唤醒全部被当前条件变量阻塞的线程

class SyncQueue
{
public:
    SyncQueue(int maxSize) : m_maxSize(maxSize) {}

    //生产者 
    void put(const int& x)
    {
        unique_lock<mutex> locker(m_mutex);
        // 判断任务队列是不是已经满了
        //循环检验：若A生产线程阻塞后 又被B消费唤醒，C生产线程也被唤醒抢先生产了配给额度 此时若为if则A线程会生产出超过m_maxSize的产品
        while (m_queue.size() == m_maxSize)  //循环检测队列是否已满 满了则条件变量m_notFull阻塞当前线程 释放当前线程占用的互斥锁locker所有权
        {
            cout << "任务队列已满, 请耐心等待..." << endl;
            // 条件变量m_notFull 阻塞当前线程  生产者的生产条件变量不为空不满足，1,阻塞当前生产线程 2,释放锁locker
            m_notFull.wait(locker);
        }
        // 将任务放入到任务队列中
        m_queue.push_back(x);
        cout << x << " 被生产" << endl;
        // 通知消费者去消费
        m_notEmpty.notify_one();//消费者的消费条件变量满足不为空 进行唤醒被m_notEmpty阻塞的消费者线程
    }

    //消费者
    int take()
    {
        unique_lock<mutex> locker(m_mutex);
        while (m_queue.empty())//循环检测队列是否为空，为空则调用m_notEmpty条件变量进行阻塞当前线程，同时释放locker所有权
        {
            cout << "任务队列已空，请耐心等待。。。" << endl;
            m_notEmpty.wait(locker);
        }
        // 从任务队列中取出任务(消费)
        int x = m_queue.front();
        m_queue.pop_front();
        // 通知生产者去生产
        m_notFull.notify_one();
        cout << x << " 被消费" << endl;
        return x;
    }

    bool empty()
    {
        lock_guard<mutex> locker(m_mutex);
        return m_queue.empty();
    }

    bool full()
    {
        lock_guard<mutex> locker(m_mutex);
        return m_queue.size() == m_maxSize;
    }

    int size()
    {
        lock_guard<mutex> locker(m_mutex);
        return m_queue.size();
    }

private:
    list<int> m_queue;     // 存储队列数据
    mutex m_mutex;         // 互斥锁
    condition_variable m_notEmpty;   // 不为空的条件变量
    condition_variable m_notFull;    // 没有满的条件变量
    int m_maxSize;         // 任务队列的最大任务个数
};

void test_condition_varable()
{
    SyncQueue taskQ(50);
    auto produce = bind(&SyncQueue::put, &taskQ, placeholders::_1);//占位符占位参数
    auto consume = bind(&SyncQueue::take, &taskQ);
    thread t1[3];
    thread t2[3];
    for (int i = 0; i < 3; ++i)
    {
        t1[i] = thread(produce, i + 100);//3个生产两个消费
        t2[i] = thread(consume);
    }

    for (int i = 0; i < 3; ++i)
    {
        t1[i].join();
        t2[i].join();
    }
}

//条件变量 condition_variable 类的 wait() 还有一个重载的方法，可以接受一个条件，这个条件也可以是一个返回值为布尔类型的函数，条件变量会先检查判断这个条件是否满足，如果满足条件（布尔值为true），则当前线程重新获得互斥锁的所有权，结束阻塞，继续向下执行；如果不满足条件（布尔值为false），当前线程会释放互斥锁（解锁）同时被阻塞，等待被唤醒。

void put(const int& x)
{
    unique_lock<mutex> locker(m_mutex);
    // 根据条件阻塞线程
    m_notFull.wait(locker, [this]() {//不需要循环检测，用第二个参数函数的返回值决定是否阻塞当前线程
        return m_queue.size() != m_maxSize;
    });
    // 将任务放入到任务队列中
    m_queue.push_back(x);
    cout << x << " 被生产" << endl;
    // 通知消费者去消费
    m_notEmpty.notify_one();
}

int take()
{
    unique_lock<mutex> locker(m_mutex);
    m_notEmpty.wait(locker, [this]() {
        return !m_queue.empty();
    });
    // 从任务队列中取出任务(消费)
    int x = m_queue.front();
    m_queue.pop_front();
    // 通知生产者去生产
    m_notFull.notify_one();
    cout << x << " 被消费" << endl;
    return x;
}

//修改之后可以发现，程序变得更加精简了，而且执行效率更高了，因为在这两个函数中的 while 循环被删掉了，但是最终的效果是一样的，推荐使用这种方式的 wait() 进行线程的阻塞。


```

![1690515151596](thread.assets/1690515151596.png)

![1698320429467](thread.assets/1698320429467.png)

## 7，线程同步之原子变量

![1651675261758](thread.assets/1651675261758.png)

![1651675736435](thread.assets/1651675736435.png)

![1651675823513](thread.assets/1651675823513.png)

![1651676349417](thread.assets/1651676349417.png)

```C++
#include <iostream>
#include <thread>
#include <atomic>
#include <functional>
using namespace std;

struct Counter
{
    void increment()
    {
        m_value.store(100);//改变值
        for (int i = 0; i < 10; ++i)
        {
            m_value++;
            cout << "increment number: " << m_value
                << ", theadID: " << this_thread::get_id() << endl;
            int value = m_value.load();//取出值
            cout << value << endl;
            this_thread::sleep_for(chrono::milliseconds(500));
        }
    }

    void decrement()
    {
        for (int i = 0; i < 10; ++i)
        {
            m_value--;
            cout << "decrement number: " << m_value
                << ", theadID: " << this_thread::get_id() << endl;
            this_thread::sleep_for(chrono::milliseconds(500));
        }
    }
    // atomic<int> == atomic_int
    atomic_int m_value = 0;//初始化
};


void Test_atmoic()
{
    Counter c;
    auto increment = bind(&Counter::increment, &c);
    auto decrement = bind(&Counter::decrement, &c);
    thread t1(increment);
    thread t2(decrement);
    t1.join();
    t2.join();
}

```

![1651676217757](thread.assets/1651676217757.png)

​	原子类型 atomic<T> 可以封装原始数据最终得到一个原子变量对象，操作原子对象能够得到和操作原始数据一样的效果，当然也可以通过 store() 和 load() 来读写原子对象内部的原始数据。

## 8，线程异步

​	C++11 中增加的线程类，使得我们能够非常方便的创建和使用线程，但有时会有些不方便，比如需要获取线程返回的结果，就不能通过 join() 得到结果，只能通过一些额外手段获得，比如：定义一个全局变量，在子线程中赋值，在主线程中读这个变量的值，整个过程比较繁琐。C++ 提供的线程库中提供了一些类用于访问异步操作的结果。

### 8.1、std::future



```C++
// ①
future() noexcept;
// ②
future( future&& other ) noexcept;
// ③
future( const future& other ) = delete;

//常用public函数
//取出 future 对象内部保存的数据，其中 void get() 是为 future<void> 准备的，此时对象内部类型就是 void，该函数是一个阻塞函数，当子线程的数据就绪后解除阻塞就能得到传出的数值了
T get();
T& get();
void get();

//因为 future 对象内部存储的是异步线程任务执行完毕后的结果，是在调用之后的将来得到的，因此可以通过调用 wait() 方法，阻塞当前线程，等待这个子线程的任务执行完毕，任务执行完毕当前线程的阻塞也就解除了。
//如果当前线程 wait() 方法就会死等，直到子线程任务执行完毕将返回值写入到 future 对象中

wait_for()； //只会让线程阻塞一定的时长，但是这样并不能保证对应的那个子线程中的任务已经执行完毕了。
wait_until() wait_for()；// 函数功能是差不多，前者是阻塞到某一指定的时间点，后者是阻塞一定的时长
void wait() const;


```

![1652099938909](thread.assets/1652099938909.png)

### 8.2 std::promise

​        std::promise 是一个协助线程赋值的类，它能够将数据和 future 对象绑定起来，为获取线程函数中的某个值提供便利。

![1652100167055](thread.assets/1652100167055.png)

![1652100278617](thread.assets/1652100278617.png)

![1652100343421](thread.assets/1652100343421.png)

```C++
void test_future()
{
    //在外部主线程中创建的 promise 对象必须要通过引用的方式传递到子线程的任务函数中；
    //在实例化子线程对象的时候，如果任务函数的参数是引用类型，那么实参一定要放到 std::ref () 函数中，表示要传递这个实参的引用到任务函数中。

    promise<int> pr;
    thread t1([](promise<int>& p) {
        p.set_value(100);//子线程内存储值 立即激活传出值，就绪
        //   p.set_value_at_thread_exit(88);//子线程内存储 子线程退出后才激活传出值，就绪
        this_thread::sleep_for(chrono::seconds(3));
        cout << "睡醒了...." << endl;
        }, ref(pr));//引用的方式传递给子线程

    future<int> f = pr.get_future();//主线程中得到future 
    int value = f.get();//get方法阻塞直到子线程数据就绪
    cout << "value: " << value << endl;
    t1.join();
}

```

![1652100946538](thread.assets/1652100946538.png)

![1652101135219](thread.assets/1652101135219.png)



### 8.3 std::packaged_task

​        std::packaged_task 类包装了一个可调用对象包装器类对象（可调用对象包装器包装的是可调用对象，可调用对象都可以作为函数来使用）。

​        这个类可以将**内部包装的函数和 future 类绑定到一起，以便进行后续的异步调用**，它和 std::promise 有点类似，s<u>td::promise 内部保存一个共享状态的值，而 std::packaged_task 保存的是一个函数。</u>

![1652103307290](thread.assets/1652103307290.png)

![1652103338697](thread.assets/1652103338697.png)

```C++
//通过 packaged_task 类包装了一个匿名函数作为子线程的任务函数，最终的得到的这个任务对象需要通过引用的方式传递到子线程内部，这样才能在主线程的最后通过任务对象得到 future 对象，再通过这个 future 对象取出子线程通过返回值传递出的数据


作者: 苏丙榅
链接: https://subingwen.cn/cpp/async/#3-2-packaged-task%E7%9A%84%E4%BD%BF%E7%94%A8
来源: 爱编程的大丙
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
void test_packaged_task()
{
    packaged_task<int(int)> task([](int x) {
        return x *= 99;
        });

    thread t1(ref(task), 10);//同样以引用的方式传递给子线程

    future<int> f = task.get_future();
    int value = f.get();//阻塞等待就绪 获取packaged_task的任务函数返回值
    cout << "value: " << value << endl;

    t1.join();
}

```

![1652103709662](thread.assets/1652103709662.png)



### 8.4 std::async

​	std::async 函数比前面提到的 std::promise 和 packaged_task 更高级一些，因为通过这函数**可以直接启动一个子线程并在这个子线程中执行对应的任务函数，异步任务执行完成返回的结果也是存储到一个 future 对象中**，当需要获取异步任务的结果时，只需要调用 future 类的get() 方法即可，如果不关注异步任务的结果，只是简单地等待任务完成的话，可以调用 future 类的wait()或者wait_for() 方法。

```c++
// 定义于头文件 <future>
// ①
template< class Function, class... Args>
std::future<std::result_of_t<std::decay_t<Function>(std::decay_t<Args>...)>>
    async( Function&& f, Args&&... args );

// ②
template< class Function, class... Args >
std::future<std::result_of_t<std::decay_t<Function>(std::decay_t<Args>...)>>
    async( std::launch policy, Function&& f, Args&&... args );


```

![1652104170354](thread.assets/1652104170354.png)

```C++
//调用 async() 函数时不指定策略就是直接创建线程并执行任务，示例代码的主线程中做了如下操作 status = f.wait_for(chrono::seconds(1)); 其实直接调用 f.get() 就能得到子线程的返回值。


void test_asyn()
{
    cout << "主线程ID: " << this_thread::get_id() << endl;
    // 调用函数直接创建线程执行任务
    future<int> f = async([](int x) {
        cout << "子线程ID: " << this_thread::get_id() << endl;
        this_thread::sleep_for(chrono::seconds(5));
        return x += 100;
        }, 100);

    future_status status;
    do {
        status = f.wait_for(chrono::seconds(1));
        if (status == future_status::deferred)
        {
            cout << "线程还没有执行..." << endl;
            f.wait();
        }
        else if (status == future_status::ready)
        {
            cout << "子线程返回值: " << f.get() << endl;
        }
        else if (status == future_status::timeout)
        {
            cout << "任务还未执行完毕, 继续等待..." << endl;
        }
    } while (status != future_status::ready);
}


```

![1652104802039](thread.assets/1652104802039.png)

```C++
//指定了 launch::deferred 策略，因此调用 async() 函数并不会创建新的线程执行任务，当使用 future 类对象调用了 get() 或者 wait() 方法后才开始执行任务（此处一定要注意调用 wait_for () 函数是不行的）。

void test_asyn()
{
    cout << "主线程ID: " << this_thread::get_id() << endl;
    // 调用函数 launch策略选择deferred 则不创建子线程 在future的get或wait时才执行task
    future<int> f = async(launch::deferred,[](int x) {
        cout << "子线程ID: " << this_thread::get_id() << endl;
        this_thread::sleep_for(chrono::seconds(5));
        return x += 100;
        }, 100);
    cout << "主线程先执行" << endl;
    cout << "子线程返回值: " << f.get() << endl;
}

```

![1652105319906](thread.assets/1652105319906.png)

![1652105391583](thread.assets/1652105391583.png)





# 四、安全性



C++11 中提供了三种智能指针，使用这些智能指针时需要引用头文件 <memory>：

std::shared_ptr：共享的智能指针
std::unique_ptr：独占的智能指针
std::weak_ptr：弱引用的智能指针，它不共享指针，不能操作资源，是用来监视 shared_ptr 的。

## 4.1 shared_ptr



### **4.1. 1 shared_ptr 的初始化**

​        智能指针是存储指向动态分配（堆）对象指针的类**，用于生存期的控制，能够确保在离开指针所在作用域时，自动地销毁动态分配的对象，防止内存泄露**。智能指针的核心实现技术是引用计数，每使用它一次，内部引用计数加1，每析构一次内部的引用计数减1，减为0时，删除所指向的堆内存。

​	共享智能指针是指多个智能指针可以同时管理同一块有效的内存，共享智能指针 shared_ptr 是一个模板类，如果要进行初始化有三种方式：**通过构造函数、std::make_shared 辅助函数以及 reset 方法**。共享智能指针对象初始化完毕之后就指向了要管理的那块堆内存，如果想要查看当前有多少个智能指针同时管理着这块内存可以使用共享智能指针提供的一个**成员函数 use_count**.

```C++
//管理当前对象的 shared_ptr 实例数量，或若无被管理对象则为 0。
long use_count() const noexcept;

```

```c++
1，构造初始化：
	shared_ptr<T> 类模板中，提供了多种实用的构造函数, 语法格式如下:
 	std::shared_ptr<T> 智能指针名字(创建堆内存);
 	如果智能指针被初始化了一块有效内存，那么这块内存的引用计数 + 1，如果智能指针没有被初始化或者被初始化为 nullptr 空指针，引用计数不会 + 1。另外，不要使用一个原始指针初始化多个 shared_ptr

2，移动构造函数和拷贝构造函数
	如果使用拷贝的方式初始化共享智能指针对象，这两个对象会同时管理同一块堆内存，堆内存对应的引用计数也会增加；如果使用移动的方式初始智能指针对象，只是转让了内存的所有权，管理内存的对象并不会增加，因此内存的引用计数不会变化。


3， 通过 std::make_shared 初始化
	template< class T, class... Args >
	shared_ptr<T> make_shared( Args&&... args );
    T：模板参数的数据类型
	Args&&... args ：要初始化的数据，如果是通过 make_shared 创建对象，需按照构造函数的参数列表指定。
    
4， 通过 reset 方法初始化
   对于一个未初始化的共享智能指针，可以通过 reset 方法来初始化，当智能指针中有值的时候，调用 reset 会使引用计数减 1。
   void reset() noexcept;

    template< class Y >
    void reset( Y* ptr );

    template< class Y, class Deleter >
    void reset( Y* ptr, Deleter d );

    template< class Y, class Deleter, class Alloc >
    void reset( Y* ptr, Deleter d, Alloc alloc );	


```

```C++
#include <memory>

class Test_Ptr
{
public:
    Test_Ptr()
    {
        cout << "construct Test..." << endl;
    }
    Test_Ptr(int x)
    {
        cout << "construct Test, x = " << x << endl;
    }
    Test_Ptr(string str)
    {
        cout << "construct Test, str = " << str << endl;
    }
    ~Test_Ptr()
    {
        cout << "destruct Test ..." << endl;
    }
};

void test_shared_ptr()
{
   //1111111---------------------------------------------------通过构造函数初始化
    // 使用智能指针管理一块 int 型的堆内存
    shared_ptr<int> ptr1(new int(520));
    cout << "ptr1管理的内存引用计数: " << ptr1.use_count() << endl;//1
    // 使用智能指针管理一块字符数组对应的堆内存
    shared_ptr<char> ptr2(new char[12]);
    cout << "ptr2管理的内存引用计数: " << ptr2.use_count() << endl;//1
    // 创建智能指针对象, 不管理任何内存
    shared_ptr<int> ptr3;
    cout << "ptr3管理的内存引用计数: " << ptr3.use_count() << endl;//0
    // 创建智能指针对象, 初始化为空
    shared_ptr<int> ptr4(nullptr);
    cout << "ptr4管理的内存引用计数: " << ptr4.use_count() << endl;//0

    //2222222-----------------------------------------------------通过拷贝和移动构造函数初始化
    // 使用智能指针管理一块 int 型的堆内存, 内部引用计数为 1
    shared_ptr<int> cptr1(new int(520));
    cout << "cptr1管理的内存引用计数: " << cptr1.use_count() << endl;//1
    //调用拷贝构造函数
    shared_ptr<int> cptr2(cptr1);
    cout << "cptr2管理的内存引用计数: " << cptr2.use_count() << endl;//2
    shared_ptr<int> cptr3 = cptr1;
    cout << "cptr3管理的内存引用计数: " << cptr3.use_count() << endl;//3
    //调用移动构造函数
    shared_ptr<int> cptr4(std::move(cptr1));
    cout << "cptr4管理的内存引用计数: " << cptr4.use_count() << endl;//3 移动构造所有权交替
    std::shared_ptr<int> cptr5 = std::move(cptr2);
    cout << "ptr5管理的内存引用计数: " << cptr5.use_count() << endl; //3 


    //333333--------------------------------------------------------通过make_shared进行初始化
     // 使用智能指针管理一块 int 型的堆内存, 内部引用计数为 1
    shared_ptr<int> sptr1 = make_shared<int>(520);
    cout << "sptr1管理的内存引用计数: " << sptr1.use_count() << endl;

    shared_ptr<Test_Ptr> sptr2 = make_shared<Test_Ptr>();
    cout << "sptr2管理的内存引用计数: " << sptr2.use_count() << endl;

    shared_ptr<Test_Ptr> sptr3 = make_shared<Test_Ptr>(520);
    cout << "sptr3管理的内存引用计数: " << sptr3.use_count() << endl;

    shared_ptr<Test_Ptr> sptr4 = make_shared<Test_Ptr>("我是要成为海贼王的男人!!!");
    cout << "sptr4管理的内存引用计数: " << sptr4.use_count() << endl;

    //44444444--------------------------------------------------------通过reset方法进行初始化
      // 使用智能指针管理一块 int 型的堆内存, 内部引用计数为 1
    shared_ptr<int> rptr1 = make_shared<int>(520);
    shared_ptr<int> rptr2 = ptr1;
    shared_ptr<int> rptr3 = ptr1;
    shared_ptr<int> rptr4 = ptr1;
    cout << "rptr1管理的内存引用计数: " << rptr1.use_count() << endl;
    cout << "rptr2管理的内存引用计数: " << rptr2.use_count() << endl;
    cout << "rptr3管理的内存引用计数: " << rptr3.use_count() << endl;
    cout << "rptr4管理的内存引用计数: " << rptr4.use_count() << endl;

    rptr4.reset();//重置，取消该指针所有权
    cout << "rptr1管理的内存引用计数: " << rptr1.use_count() << endl;
    cout << "rptr2管理的内存引用计数: " << rptr2.use_count() << endl;
    cout << "rptr3管理的内存引用计数: " << rptr3.use_count() << endl;
    cout << "rptr4管理的内存引用计数: " << rptr4.use_count() << endl;//0


    shared_ptr<int> ptr5;
    ptr5.reset(new int(250));
    cout << "ptr5管理的内存引用计数: " << ptr5.use_count() << endl;
    *ptr5 = *ptr5 + 50;//针对基础可以和普通指针一样使用，对于对象则要get返回的指针使用
    cout << "ptr5管理的内存值: " << *ptr5 << endl;
    //-------------------------------------------------get获取原始内存地址
    int len = 128;
    shared_ptr<char> ptr(new char[len]);
    // 得到指针的原始地址
    char* add = ptr.get();
    memset(add, 0, len);
    strcpy_s(add, 128,"我是要成为海贼王的男人!!!");
    cout << "string: " << add << endl;

    shared_ptr<int> p(new int);
    *p = 100;
    cout << *p.get() << "  " << *p << endl;
}

```

![1652276933100](thread.assets/1652276933100.png)

### 4.1.2  获取原始指针

​	对应基础数据类型来说，通过操作**智能指针和操作智能指针管理的内存效果是一样的，可以直接完成数据的读写**。但是如果<u>共享智能指针管理的是一个对象，那么就需要取出原始内存的地址再操作</u>，可以调用共享智能指针类提供的 get () 方法得到原始地址.

​     T* get() const noexcept

```C++
   void test(){
   int len = 128;
    shared_ptr<char> ptr(new char[len]);
    // 得到指针的原始地址
    char* add = ptr.get();
    memset(add, 0, len);
    strcpy_s(add, 128,"我是要成为海贼王的男人!!!");
    cout << "string: " << add << endl;

    shared_ptr<int> p(new int);
    *p = 100;
    cout << *p.get() << "  " << *p << endl;
    }

```



![1652276717580](thread.assets/1652276717580.png)



### 4.1.3 指定删除器



​	当智能指针管理的内存对应的引用计数变为 0 的时候，这块内存就会被智能指针析构掉了。另外，我们在初始化智能指针的时候也可以自己指定删除动作，这个删除操作对应的函数被称之为删除器，这个删除器函数本质是一个**回调函数，我们只需要进行实现，其调用是由智能指针完成的**。

 

```c++
// 自定义删除器函数，释放int型内存
void deleteIntPtr(int* p)
{
    delete p;
    cout << "int 型内存被释放了...";
}


//数组封装
template <typename T>
shared_ptr<T> make_share_array(size_t size)
{
    // 返回匿名对象
    return shared_ptr<T>(new T[size], default_delete<T[]>());
}


int main()
{
    shared_ptr<int> ptr(new int(250), deleteIntPtr);
    shared_ptr<int> ptr2(new int(250), [](int* p) {delete p; });//可以lambda表达式
    //在 C++11 中使用 shared_ptr 管理动态数组时，需要指定删除器，因为 std::shared_ptr的默认删除器不支持数组对象
    shared_ptr<int> ptr(new int[10], [](int* p) {delete[]p; });
    
    //std::default_delete<T>() 函数作为删除器，这个函数内部的删除功能也是通过调用 delete 来实现的，要释放什么类型的内存就将模板类型 T 指定为什么类型即可
    shared_ptr<int> ptr(new int[10], default_delete<int[]>());
    
    //利用封装简化数组指针
    shared_ptr<int> ptr1 = make_share_array<int>(10);
    cout << ptr1.use_count() << endl;
    return 0;
}


```



## 4.2 unique_ptr

​	std::unique_ptr 是一个独占型的智能指针，它不允许其他的智能指针共享其内部的指针，可以通过它的构造函数初始化一个独占智能指针对象，但是不允许通过赋值将一个 unique_ptr 赋值给另一个 unique_ptr。

```C++
 // 通过构造函数初始化对象

unique_ptr<int> ptr1(new int(10));

// error, 不允许将一个unique_ptr赋值给另一个unique_ptr

unique_ptr<int> ptr2 = ptr1;


```


​	std::unique_ptr **不允许复制**，但是可以通过**函数返回给其他的 std::unique_ptr，还可以通过 std::move 来转译给其他的 std::unique_ptr**，这样原始指针的所有权就被转移了，这个原始指针还是被独占的

```C++
unique_ptr<int> func()
{
    return unique_ptr<int>(new int(520));
}

void Test_UniquePtr()
{
    // 通过构造函数初始化
    unique_ptr<int> ptr1(new int(10));
    // 通过转移所有权的方式初始化
    unique_ptr<int> ptr2 = move(ptr1);
    unique_ptr<int> ptr3 = func();

    unique_ptr<int> ptr4 = move(ptr3);

    cout << *ptr2 << " " << *ptr4<< endl;//ptr1和ptr3通过move失去对内存的所有权
    //使用 reset 方法可以让 unique_ptr 解除对原始内存的管理，也可以用来初始化一个独占的智能指针。
    ptr1.reset();
    ptr2.reset(new int(250));
    
    //get获取原始地址
    cout <<* (ptr2.get()) <<" "<< * ptr4 << endl;

}


```

![1652598000885](thread.assets/1652598000885.png)

**删除器**

unique_ptr 指定删除器和 shared_ptr 指定删除器是有区别的，unique_ptr 指定删除器的时候<u>需要确定删除器的类型，所以不能像 shared_ptr 那样直接指定删除器。</u>

   	

```C++
shared_ptr<int> ptr1(new int(10), [](int*p) {delete p; });	// ok
unique_ptr<int> ptr1(new int(10), [](int*p) {delete p; });	// error

//一般使用函数包装器function进行包装最为稳妥
 unique_ptr<int, function<void(int*)>> ptr1(new int(10), [&](int*p) {delete p; });

```

## 4.3 weak_ptr



std::weak_ptr：弱引用的智能指针，它不共享指针，不能操作资源，是用来监视 shared_ptr 的。

弱引用智能指针 std::weak_ptr 可以看做是 *<u>shared_ptr 的助手，它不管理 shared_ptr 内部的指针。</u>*std::weak_ptr 没有重载操作符 * 和 ->，因为它不共享指针，不能操作资源，所以它的构造不会增加引用计数，析构也不会减少引用计数，它的主要作用就是**作为一个旁观者监视 shared_ptr 中管理的资源是否存在**。

```C++
void test_weak_ptr()
{
    shared_ptr<int> sp(new int);

    weak_ptr<int> wp1;
    weak_ptr<int> wp2(wp1);//weak_ptr（weak_ptr shared_ptr）= weak_ptr shared_ptr 拷贝构造和赋值拷贝
    weak_ptr<int> wp3(sp);
    weak_ptr<int> wp4;
    wp4 = sp;
    weak_ptr<int> wp5;
    wp5 = wp3;

    cout << "use_count: " << endl;
    cout << "wp1: " << wp1.use_count() << endl;//观测资源的引用计数
    cout << "wp2: " << wp2.use_count() << endl;
    cout << "wp3: " << wp3.use_count() << endl;
    cout << "wp4: " << wp4.use_count() << endl;
    cout << "wp5: " << wp5.use_count() << endl;

    cout << "3. weak " << (wp3.expired() ? "is" : "is not") << " expired" << endl;//bool expired()返回是否所观测对象释放
    wp3.reset();//reset使得wp1不监测任何对象
    cout << "3. weak " << (wp3.expired() ? "is" : "is not") << " expired" << endl;
    cout << "4. weak " << (wp4.expired() ? "is" : "is not") << " expired" << endl;

    //shared_ptr<element_type> lock() const noexcept 返回shared_ptr
    shared_ptr<int> sp2 = wp4.lock();
    *sp2 = 99;
    sp.reset();//共享指针reset不管理任何对象
    cout << "4. weak " << (wp4.expired() ? "is" : "is not") << " expired" << endl;
    cout << "sp2:" << *sp2 << endl;
}

```

![1652600163661](thread.assets/1652600163661.png)

**返回管理this的职能指针：**

![1652600999290](thread.assets/1652600999290.png)

​	通过输出的结果可以看到一个对象被析构了两次，其原因是这样的：在这个例子中使用**同一个指针 this 构造了两个智能指针对象 sp1 和 sp2，这二者之间是没有任何关系的**，各自不知道对方存在，认为计数1。因为 sp2 并不是通过 sp1 初始化得到的实例对象。**在离开作用域之后 this 将被构造的两个智能指针各自析构，导致重复析构的错误。**

​    这个问题可以通过 weak_ptr 来解决，**通过 wek_ptr 返回管理 this 资源的共享智能指针对象 shared_ptr**。C++11 中为我们提供了一个模板类叫做 **std::enable_shared_from_this<T>**，这个类中有一个方法叫做 **shared_from_this()**，通过这个方法可以返回一个共享智能指针，**在函数的内部就是使用 weak_ptr 来监测 this 对象，并通过调用 weak_ptr 的 lock() 方法返回一个 shared_ptr 对象**

![1652601364743](thread.assets/1652601364743.png)




![1652601390719](thread.assets/1652601390719.png)

# 五、通用性的提升

## 5.1 const constexpr

const 关键字，从功能上来说这个关键字有双重语义：变量只读，修饰常量。

## 5.2 委托构造函数和继承构造函数

委托构造函数允许使用同一个类中的一个构造函数调用其它的构造函数，从而简化相关变量的初始化。



```c++
class Test

{

public:

    Test() {};
    Test(int max)

    {

        this->m_max = max > 0 ? max : 100;

    }
    Test(int max, int min)
    {
        this->m_max = max > 0 ? max : 100;              // 冗余代码
        this->m_min = min > 0 && min < max ? min : 1;   
    }

    Test(int max, int min, int mid)
    {
        this->m_max = max > 0 ? max : 100;             // 冗余代码
        this->m_min = min > 0 && min < max ? min : 1;  // 冗余代码
        this->m_middle = mid < max && mid > min ? mid : 50;
    }

    int m_min;
    int m_max;
    int m_middle;
}

//委托构造函数
class Test
{
public:
    Test() {};
    Test(int max)
    {
        this->m_max = max > 0 ? max : 100;
    }

    Test(int max, int min):Test(max)//要放到初始化列表中进行调用，避免提示max重复定义
    {
        this->m_min = min > 0 && min < max ? min : 1;
    }

    Test(int max, int min, int mid):Test(max, min)
    {
        this->m_middle = mid < max && mid > min ? mid : 50;
    }

    int m_min;
    int m_max;
    int m_middle;
};



```

![1653204891223](Cplusplus11.assets\1653204891223.png

![1653205071530](thread.assets/1653205071530.png)

C++11 中提供的继承构造函数**可以让派生类直接使用基类的构造函数，而无需自己再写构造函数**，尤其是在基类有很多构造函数的情况下，可以极大地简化派生类构造函数的编写。

```C++
class Base
{
public:
    Base(int i) :m_i(i) {}
    Base(int i, double j) :m_i(i), m_j(j) {}
    Base(int i, double j, string k) :m_i(i), m_j(j), m_k(k) {}

    int m_i;
    double m_j;
    string m_k;
};

class Child : public Base
{
public:
    Child(int i) :Base(i) {}
    Child(int i, double j) :Base(i, j) {}
    Child(int i, double j, string k) :Base(i, j, k) {}
};

int main()
{
    Child c(520, 13.14, "i love you");
    cout << "int: " << c.m_i << ", double: " 
         << c.m_j << ", string: " << c.m_k << endl;
    return 0;
}


```

​	在子类中初始化从基类继承的类成员，需要在子类中重新定义和基类一致的构造函数，这是非常繁琐的，C++11 中通过添加继承构造函数这个新特性完美的解决了这个问题，使得代码更加精简。

​	继承构造函数的使用方法是这样的：**通过使用 using 类名::构造函数名**（其实类名和构造函数名是一样的）来声明使用基类的构造函数，这样子类中就可以不定义相同的构造函数了，直接使用基类的构造函数来构造派生类对象.

```c++
class Base
{
public:
    Base(int i) :m_i(i) {}
    Base(int i, double j) :m_i(i), m_j(j) {}
    Base(int i, double j, string k) :m_i(i), m_j(j), m_k(k) {}

    int m_i;
    double m_j;
    string m_k;
};

class Child : public Base
{
public:
    using Base::Base;
};

int main()
{
    Child c1(520, 13.14);
    cout << "int: " << c1.m_i << ", double: " << c1.m_j << endl;
    Child c2(520, 13.14, "i love you");
    cout << "int: " << c2.m_i << ", double: " 
         << c2.m_j << ", string: " << c2.m_k << endl;
    return 0;
}



```

另外如果在子类中隐藏了父类中的同名函数，也可以通过 using 的方式在子类中使用基类中的这些父类函数：

```C++
class Base
{
public:
    Base(int i) :m_i(i) {}
    Base(int i, double j) :m_i(i), m_j(j) {}
    Base(int i, double j, string k) :m_i(i), m_j(j), m_k(k) {}

    void func(int i)
    {
        cout << "base class: i = " << i << endl;
    }
    
    void func(int i, string str)
    {
        cout << "base class: i = " << i << ", str = " << str << endl;
    }

    int m_i;
    double m_j;
    string m_k;
};

class Child : public Base
{
public:
    using Base::Base;
    using Base::func;
    void func()
    {
        cout << "child class: i'am luffy!!!" << endl;
    }
};

int main()
{
    Child c(250);
    c.func();
    c.func(19);
    c.func(19, "luffy");
    return 0;
}

```

​	子类中的 func() 函数隐藏了基类中的两个 func() 因此默认情况下通过子类对象只能调用无参的 func()，在上面的子类代码中添加了 using Base::func; 之后，就可以通过子类对象直接调用父类中被隐藏的带参 func() 函数了。

## 5.3 右值引用

https://blog.csdn.net/weixin_44788542/article/details/126284429

### 5.3.1 右值引用



C++11 增加了一个新的类型，称为右值引用（ R-value reference），标记为 &&。

lvalue 是 loactor value 的缩写，rvalue 是 read value 的缩写

**左值是指存储在内存中、有明确存储地址（可取地址）的数据；**

**右值是指可以提供数据值的数据（不可取地址）；**

​	区分左值与右值的便捷方法是：可以对表达式取地址（&）就是左值，否则为右值 。所有有名字的变量或对象都是左值，而右值是匿名的。

![1653210783074](thread.assets/1653210783074.png)


![1653210863186](thread.assets/1653210863186.png)

```C++
nt&& value = 520;
class Test
{
public:
    Test()
    {
        cout << "construct: my name is jerry" << endl;
    }
    Test(const Test& a)
    {
        cout << "copy construct: my name is tom" << endl;
    }
};

Test getObj()
{
    return Test();
}

int main()
{
    int a1;
    int &&a2 = a1;        // error  a1左值
    Test& t = getObj();   // error
    Test && t = getObj();//非引用返回，即右值 getObj() 返回的临时对象被称之为将亡值，t 是这个将亡值的右值引用
    const Test& t = getObj();//常量左值引用是一个万能引用类型，它可以接受左值、右值、常量左值和常量右值。
    return 0;
}


```

### 5.3.2 性能优化

​	在 C++ 中在进行对象赋值操作的时候，很多情况下会发生**对象之间的深拷贝**，如果堆内存很大，这个拷贝的代价也就非常大，在某些情况下，如果想要避免对象的深拷贝，就可以使用右值引用进行性能的优化。

```C++
class Testrvalue
{
public:
    Testrvalue() : m_num(new int(100))
    {
        cout << "construct: my name is jerry" << endl;
    }
    //常量左值引用的拷贝构造函数
    Testrvalue(const Testrvalue& a) : m_num(new int(*a.m_num))
    {
        cout << "copy construct: my name is tom" << endl;
    }

    // 添加移动构造函数
    Testrvalue(Testrvalue&& a) : m_num(a.m_num)
    {
        a.m_num = nullptr;
        cout << "move construct: my name is sunny" << endl;
    }

    ~Testrvalue()
    {
        delete m_num;
        cout << "destruct Test class ..." << endl;
    }

    int* m_num;
};

Testrvalue getObj()
{
    Testrvalue t;
    return t;//返回时会调用拷贝构造函数
}
void testravue()
{
    Testrvalue t = getObj();
    cout << "t.m_num: " << *t.m_num << endl;
}

```

![1653212590845](thread.assets/1653212590845.png)



![1653212540163](thread.assets/1653212540163.png)

### 5.3.3 &&的特性

​	在 C++ 中，并不是所有情况下 && 都代表是一个右值引用，具体的场景体现在模板和自动类型推导中，如果是模板参数需要指定为 **T&&，如果是自动类型推导需要指定为 auto &&，在这两种场景下 && 被称作未定的引用类型。另外还有一点需要额外注意 const T&& 表示一个右值引用，不是未定引用类型。**

​	![1653213315339](thread.assets/1653213315339.png)

![1653213365820](thread.assets/1653213365820.png)

![1653213412008](thread.assets/1653213412008.png)

![1653213503435](thread.assets/1653213503435.png)

```C++
void printValue(int &i)
{
    cout << "l-value: " << i << endl;
}

void printValue(int &&i)
{
    cout << "r-value: " << i << endl;
}

void forward(int &&k)
{
    printValue(k);
}

int main()
{
    int i = 520;
    printValue(i);
    printValue(1314);
    forward(250);

    return 0;
};


```

![1653213701311](thread.assets/1653213701311.png)

## 5.4 完美转发

![1698391937222](../../Qt学习/Cpluss11Traicts/Cplusplus11.assets/1698391937222.png)

![1698391985941](thread.assets/1698391985941.png)

![1698392107899](thread.assets/1698392107899.png)

![1698392908881](thread.assets/1698392908881.png)

![1698393509399](thread.assets/1698393509399.png)

![1698393518985](../../Qt学习/Cpluss11Traicts/Cplusplus11.assets/1698393518985.png)

## 5.5 列表初始化

### 5.5.1统一初始化

![1698650144663](thread.assets/1698650144663.png)

![1698650158659](thread.assets/1698650158659.png)

![1698650167965](thread.assets/1698650167965.png)

![1698650314440](thread.assets/1698650314440.png)

![1698650568863](thread.assets/1698650568863.png)

### 5.5.2聚合体与非聚合体

![1698651208960](thread.assets/1698651208960.png)

![1698651229989](thread.assets/1698651229989.png)

![1698651389396](thread.assets/1698651389396.png)

![1698651407357](thread.assets/1698651407357.png)

![1698651725915](thread.assets/1698651725915.png)

![1698651857215](thread.assets/1698651857215.png)

![1698652016072](../../Qt学习/Cpluss11Traicts/Cplusplus11.assets/1698652016072.png)

### 5.5.3 std::initializer_list

![1698652296081](thread.assets/1698652296081.png)

5.5.3.1 作为普通函数参数

定义可以接受任意个数参数的变参函数，只需要将std::initializer_list 作为参数，使用初始化列表{}作为实参进行数据传递。

![1698652759694](thread.assets/1698652759694.png)

![1698652771190](thread.assets/1698652771190.png)

### 5.5.3.2 作为构造函数的参数

![1698652917905](thread.assets/1698652917905.png)

## 5.6 using 使用

### 5.6.1 定义别名

![1698653760332](../../Qt学习/Cpluss11Traicts/Cplusplus11.assets/1698653760332.png)

![1698653822507](thread.assets/1698653822507.png)

### 5.6.2模板别名

![1698653916347](thread.assets/1698653916347.png)

![1698654841453](thread.assets/1698654841453.png)

![1698654949482](thread.assets/1698654949482.png)



5.10 friend

![1698668437242](thread.assets/1698668437242.png)

![1698668540919](thread.assets/1698668540919.png)

![1698668610522](thread.assets/1698668610522.png)

![1698668756203](thread.assets/1698668756203.png)

## 5.11强类型枚举

![1686557500387](thread.assets/1686557500387.png)

![1686557659291](../../Qt学习/Cpluss11Traicts/Cplusplus11.assets/1686557659291.png)

![1686557747297](thread.assets/1686557747297.png)

![1686557828288](../../Qt学习/Cpluss11Traicts/Cplusplus11.assets/1686557828288.png)

![1686558099085](thread.assets/1686558099085.png)

![1686558160579](thread.assets/1686558160579.png)

# 六、其他

## 6.1 可变参数模板

### 6.1.1 可变参数

![1698823483894](thread.assets/1698823483894.png)

![1698823498791](thread.assets/1698823498791.png)

![1698825451417](thread.assets/1698825451417.png)

```c++
#include <stdarg.h>
#pragma execution_character_set("utf-8");

void myprint(char*fmt,...)
{
	va_list ap;
	int d;
	char c;
	char*s;
	std::string fmtstr = fmt;
	std::string outstr;
	va_start(ap, fmt);//锚定第一个可变参数位置
	for (int i = 0; i < fmtstr.size(); ++i)
	{
		if (fmtstr[i] == '%'&& i + 1< fmtstr.size())
		{
			switch (fmtstr[i + 1])
			{
				case 's':
				{
					s = va_arg(ap, char*);//获取参数 需要指定类型（指针移动需要）
					outstr += s;
					++i;
					break;
				}
				case 'd':
				{
					d = va_arg(ap,int);
					outstr += std::to_string(d);
					++i;
					break;
				}
				case 'c':
				{
					c = va_arg(ap, char);
					outstr += std::string(1,c);
					++i;
					break;
				}
				default:
				{
					outstr += fmtstr[i];
					break;
				}
			}
		}
		else
		{
			outstr += fmtstr[i];
		}
	}
	va_end(ap);//va_start结束 释放可能动态申请的内存
	std::cout << outstr;
}

myprint("Test my print,name:%s,age:%d,BloodType:%c", "LiSi", 28, 'A');

```

![1698823636573](thread.assets/1698823636573.png)

### 6.1.2 C++可变参数模板

