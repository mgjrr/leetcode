
    交替打印01020304,输出2n长度,写的很丑
    卡了很久,就是注意要线程之间互相告知对方退出.

```
class ZeroEvenOdd {
private:
    int n;

public:
    ZeroEvenOdd(int n) {
        this->n = n;
    }
    mutex mtx;
    int c0 = 1,c1 = 0,c2 = 0,x = 0;
    condition_variable cv0,cv1,cv2;
    // printNumber(x) outputs "x", where x is an integer.
    void zero(function<void(int)> printNumber) {
        while(x<=n)
        {   
            if(x==n) break;
            unique_lock<mutex> lck(mtx);
            while(!c0)
            {
                cv0.wait(lck);
            }
            c0 = 0;
            x++;
            if(x&1)
            {
                c1 = 1;
                cv1.notify_one();
            }
            else
            {
                c2 = 1;
                cv2.notify_one();
            }
            printNumber(0);
        }
    }

    void even(function<void(int)> printNumber) {
        while(x<=n)
        {
            unique_lock<mutex> lck(mtx);
            while(!c2)
            {
                cv2.wait(lck);
            }
            if(x>n) break;
            printNumber(x);
            c2 = 0;
            c0 = 1;
            cv0.notify_one();
            if(x==n) break;
        }
        x = 1000000;
        c1 = 1;
        cv1.notify_all();
    }

    void odd(function<void(int)> printNumber) {
        while(x<=n)
        {
            unique_lock<mutex> lck(mtx);
            while(!c1)
            {
                cv1.wait(lck);
            }
            if(x>n) break;
            c1 = 0;
            c0 = 1;
            printNumber(x);
            cv0.notify_one();
            if(x==n) break;
        }
        c2 = 1;
        x = 1000000;
        cv2.notify_all();
    }
};
```
