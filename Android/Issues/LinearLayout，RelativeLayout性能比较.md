* RelativeLayout会让子View调用2次onMeasure，LinearLayout 在有weight时，也会调用子View2次onMeasure

* RelativeLayout的子View如果高度和RelativeLayout不同，则会引发效率问题，当子View很复杂时，这个问题会更加严重。如果可以，子View尽量使用padding代替margin。

* 在**不影响层级深度**的情况下,使用LinearLayout和FrameLayout而不是RelativeLayout。
