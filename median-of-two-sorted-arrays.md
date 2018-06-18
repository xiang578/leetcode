# 两个排序数组的中位数

> 给定两个大小为 m 和 n 的有序数组 nums1 和 nums2 。
请找出这两个有序数组的中位数。要求算法的时间复杂度为 O(log (m+n)) 。
示例 1:
nums1 = [1, 3]
nums2 = [2]
中位数是 2.0
示例 2:
nums1 = [1, 2]
nums2 = [3, 4]
中位数是 (2 + 3)/2 = 2.5

题目中要求的复杂度是${log(n+m)}$，数组又是有序的，自然而然想到去二分，而且${\log (n+m)=\log (n)*\log(m)}$，看起来只要对第一个数组二分再对第二个数组二分就能解决问题了。刚开始就顺着这样的思路想下去。如果没有时间复杂度的限制，做这个题目最简单的方式，将这两个数组 nums1（记元素个数为n) 和 nums2(记元素个数为m） 归并成一个新的数组 nums。得到两种情况：
    
    1. 对于 n+m 为奇数时，中位数为nums[(n+m)/2]
    2. 对于 n+m 为偶数时，中位数为(nums[(n+m)/2]+nums[(n+m)/2-1])/2

然后从这个关系中考虑如何进行二分，我们可以考虑 nums 中的前 (n+m)/2 个元素，是从 nums1 中取前 x 个元素，再从 nums2 中取前 y 个元素得到的。所以，我们可以去二分+验证nums1 中 x 的取法。

x 的范围在 [0,n] 之间，y 的大小正好是 (n+m)/2-x，y 的大小也有限制即 [0,m]。然后验证的时候也很简单，只要判断a[x]与b[y]（数组下标从0开始，所以取出来的元素为 nums1[0]...nums1[x-1])是否大于全部取出的元素。如果是那么就得到了答案。如果不是，就可以根据结果调整二分的范围。

这个题的细节考虑有点多，需要注意。看了几分标程，也可以用考虑数组第 k 大的方法来找答案。

    
```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int n = nums1.size();
        int m = nums2.size();
        if(n==0&&m==0) return 0;
        if(n==0){
            if(m%2) return nums2[m/2];
            else return (nums2[m/2-1]+nums2[m/2])*0.5;
        }
        if(m==0){
            // cout<<(nums1[n/2-1]+nums1[n/2])<<endl;
            if(n%2) return nums1[n/2];
            else return (nums1[n/2-1]+nums1[n/2])*0.5;
        }
      
      
        int l=0,r=n;
        int ok = 0;
        double ans = 0;
        while(!ok){
            int x = (l+r)/2;
            int y = (n+m)/2+1-x;
            if(y>m) 
            {
                l=x+1;
                continue;
            }
            if(y<0)
            {
                r=x-1;
                continue;
            }
            vector<int>t;
            if(x-1>=0) t.push_back(nums1[x-1]);
            if(x-2>=0) t.push_back(nums1[x-2]);
            if(y-1>=0) t.push_back(nums2[y-1]);
            if(y-2>=0) t.push_back(nums2[y-2]);
            sort(t.begin(),t.end());
            int p=0x7fffffff;
            if(x<n) p = min(nums1[x],p);
            if(y<m) p = min(nums2[y],p);
            // printf("%d %d %d %d\n",l,r,x,y);
            // for(int i = 0;i<t.size();i++)
            //   printf("%d\n",t[i]);
            if(p>=t[t.size()-1]){
                ok = 1;
                if((n+m)%2==0) ans = (t[t.size()-1] + t[t.size()-2])*0.5;
                else ans = t[t.size()-1];
            }
            else{
                if(x>=n) {
                    r=x-1;
                }
                else if(y>=m){
                    l=x+1;
                }
                else{
                    if(nums1[x]>nums2[y]){
                        r=x-1;
                    }
                    else{
                        l=x+1;
                    }
                }
            }
        }
        return ans;
    }
};
```


