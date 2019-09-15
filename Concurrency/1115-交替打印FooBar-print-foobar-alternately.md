    简单消费生产者问题.

```
class FooBar {
private:
    int n;
    mutex mtx;
    int cnt = 0;
    condition_variable cv0,cv1;
public:
    FooBar(int n) {
        this->n = n;
    }

    void foo(function<void()> printFoo) {
        
        for (int i = 0; i < n; i++) {
            unique_lock<mutex> lck(mtx);
            while(cnt)
            {
                cv0.wait(lck);
            }
            cnt++;
        	// printFoo() outputs "foo". Do not change or remove this line.
        	printFoo();
            cv1.notify_one();
        }
    }

    void bar(function<void()> printBar) {
        
        for (int i = 0; i < n; i++) {
            unique_lock<mutex> lck(mtx);
            while(!cnt)
            {
                cv1.wait(lck);
            }
            cnt--;
        	// printFoo() outputs "foo". Do not change or remove this line.
        	printBar();
            cv0.notify_one();
        	// printBar() outputs "bar". Do not change or remove this line.
        }
    }
};
```
