# RollingNumber
数字滚动的效果，应该在网页中很常见
#实现的效果:


![Image text](https://img-blog.csdn.net/20180510165251647)
# 实现代码如下，Demo我就不上传了
#

 
 
class NumberAnimationTextView(context: Context) : AppCompatTextView(context) {


    //默认开始的数字
    private var mStartNum:String="0"
    //结束时的数字
     var mEndNum:String?=null

    //设置动画的事件
    private var duration:Long=3000

    //判断是否是正数
    private var isInt:Boolean=false

    //设置动画执行事件，默认为3000
    public fun setDuration(duration:Long){
        this.duration=duration
    }

    //设置结束的数字(默认开始的数字是0)
    public fun setNumber(endNumber: String){
        setNumber("0",endNumber)
    }

    //设置开始的数字和结束的数字
    public fun setNumber(startNumber:String,endNumber:String){
        mStartNum=startNumber
        mEndNum=endNumber

        //检查数字的合法性
        if (checkNum(startNumber,endNumber)){
            //如果数字合法,开始动画
            startNumAnimation()
        }else{
            //数字不合法，直接显示数字
            setText(endNumber)
        }


    }

    //数字滚动动画
    private fun startNumAnimation() {
        var valueAnimator:ValueAnimator= ValueAnimator.ofObject(
                BigDecimalEvaluator(),
                BigDecimal(mStartNum),
                BigDecimal(mEndNum))
        valueAnimator.setDuration(duration)
        valueAnimator.setInterpolator(AccelerateDecelerateInterpolator())
        valueAnimator.addUpdateListener(object :ValueAnimator.AnimatorUpdateListener{
            override fun onAnimationUpdate(animation: ValueAnimator?) {
                var value:BigDecimal=animation?.getAnimatedValue() as BigDecimal
                setText(""+format(value)+"")
            }

        })

        valueAnimator.start()
    }

    //格式化BigDecimal
    private fun format(bigDecimal:BigDecimal):String{
        var pattern:String?=null
        if (isInt){
            //如果是整数
            pattern="#,###"
        }else{
            //表示小数
            pattern="#,##0.00"
        }
        //进行格式化
        val decimalFomat:DecimalFormat= DecimalFormat(pattern)
        //返回格式化以后的字符串
        return decimalFomat.format(bigDecimal)

    }

    //计算线性插值器的结果
    class BigDecimalEvaluator: TypeEvaluator<Any>{
        override fun evaluate(fraction: Float, startValue: Any?, endValue: Any?): Any {
            val start:BigDecimal=startValue as BigDecimal
            val end:BigDecimal=endValue as BigDecimal
            val result:BigDecimal=end.subtract(start)

            return result.multiply(BigDecimal(""+fraction)).add(start)

        }


    }

    //校验数字是否合法
    private fun checkNum(startNum:String,endNum:String):Boolean{
        try {
            BigInteger(startNum)
            BigInteger(endNum)
            isInt=true
        }catch (e:Exception){
            isInt=false
            e.printStackTrace()
        }

        try {
            //起始数字小数的筛选
            var start:BigDecimal=BigDecimal(startNum)
            var end:BigDecimal= BigDecimal(endNum)

            return end.compareTo(start)>=0
        }catch (e:Exception){
            e.printStackTrace()
            return false
        }
    }





}
