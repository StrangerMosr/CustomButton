# -Button-XML-
一个带有点击效果和计时功能可圆角的自定义Button（支持XML和代码修改属性）
由于实际开发中我们需要给很多按钮添加点击效果，按钮的点击效果添加的方法大致两种：
一、用selector写的XML，二、利用onTouch来写；本控件实现就是利用第二种。
继承Button，利用attrs给View添加自定义属性，onTouch给按钮添加点击效果，运用GradientDrawable 给View添加圆角，CountDownTimer 给View增加计时功能
[java] view plain copy
package com.example.test;  
  
import android.annotation.SuppressLint;  
import android.content.Context;  
import android.content.res.TypedArray;  
import android.graphics.Color;  
import android.graphics.drawable.GradientDrawable;  
import android.os.CountDownTimer;  
import android.text.Spannable;  
import android.text.SpannableString;  
import android.text.TextUtils;  
import android.text.style.ForegroundColorSpan;  
import android.util.AttributeSet;  
import android.view.Gravity;  
import android.view.MotionEvent;  
import android.view.View;  
import android.widget.Button;  
import android.widget.TextView;  
  
/** 
 * 重写Button,自定义Button样式 可在代码中或者XML文件中设置按钮的形状（是否圆角） 设置背景 设置文字颜色 
 * 设置点击效果(color支持String和Resources ID)等 
 *  
 * @author Scw 
 * @date 2015-12-1 
 */  
public class CustomButton extends Button {  
    private boolean fillet = false;// 是否设置圆角  
    private float radius = 8;// 圆角半径  
    private int shape = 0;// 圆角样式，矩形、圆形等，由于矩形的Id为0，默认为矩形  
    private int backColori = 0;// 背景色，int类型  
    private int backColorSelectedi = 0;// 按下后的背景色，int类型  
    private int backGroundImage = 0;// 背景图，只提供了Id  
    private int backGroundImageSeleted = 0;// 按下后的背景图，只提供了Id  
    private int textColori = 0;// 文字颜色，int类型  
    private int textColorSeletedi = 0;// 按下后的文字颜色，int类型  
    private String backColors = "";// 背景色，String类型  
    private String textColors = "";// 文字颜色，String类型  
    private String textColorSeleteds = "";// 按下后的文字颜色，String类型  
    private String backColorSelecteds = "";// 按下后的背景色，String类型  
    private GradientDrawable gradientDrawable;// 控件的样式  
    private TimeLastUtil time;  
    private boolean isStartTime = false;  
    private boolean timeIsEnd = true;  
    public CustomButton(Context context, AttributeSet attrs, int defStyle) {  
    super(context, attrs, defStyle);  
    init(context, attrs);  
    }  
  
    public CustomButton(Context context, AttributeSet attrs) {  
    this(context, attrs, 0);  
    init(context, attrs);  
    }  
  
    public CustomButton(Context context) {  
    this(context, null);  
    }  
  
    @SuppressLint({ "ClickableViewAccessibility", "Recycle" })  
    private void init(Context context, AttributeSet attrs) {  
    time = new TimeLastUtil(10000, 1000, this, "S后重新发送");  
    // 设置Touch事件  
    setOnTouchListener(new OnTouchListener() {  
        @Override  
        public boolean onTouch(View arg0, MotionEvent event) {  
        // 按下改变样式  
        setColor(event, arg0);  
        if (isStartTime && timeIsEnd) {  
            timeIsEnd = false;  
            time.start();  
        }  
        return true;  
        }  
    });  
    /* 
     * 使用resalues/attrs.xml中的<declare- styleable>定义的Gallery属性. 
     */  
    /* 取得CustomButon属性的Index id */  
    TypedArray typearray = context.obtainStyledAttributes(attrs, R.styleable.CustomButton);  
    /* 让对象的styleable属性能够反复使用 */  
    setBackColor(typearray.getColor(R.styleable.CustomButton_customBackground, 0));  
    setBackColorSelected(typearray.getColor(R.styleable.CustomButton_clickeffect, 0));  
    setFillet(typearray.getBoolean(R.styleable.CustomButton_fillet, fillet));  
    setTextColori(typearray.getColor(R.styleable.CustomButton_customTextColor, Color.WHITE));  
    setTextColorSelected(typearray.getColor(R.styleable.CustomButton_textclickeffect, 0));  
    setRadius(typearray.getFloat(R.styleable.CustomButton_customRadius, radius));  
    isStartTime=typearray.getBoolean(R.styleable.CustomButton_isStartTimer, isStartTime);  
    // 将Button的默认背景色改为透明（透明色的资源ID无法获取）  
    if (gradientDrawable == null) {  
        gradientDrawable = new GradientDrawable();  
    }  
    if (0 == typearray.getInt(R.styleable.CustomButton_customBackground, 0)) {  
        gradientDrawable.setColor(Color.TRANSPARENT);  
    } else {  
        setBackColor(typearray.getInt(R.styleable.CustomButton_customBackground, 0));  
    }  
    // 设置文字默认居中  
    setGravity(Gravity.CENTER);  
    typearray.recycle();  
    }  
  
    public boolean setColor(MotionEvent event, View arg0) {  
    if (MotionEvent.ACTION_DOWN == event.getAction()) {  
        // 按下  
        if (0 != backColorSelectedi) {  
        // 先判断是否设置了按下后的背景色int型  
        if (fillet) {  
            if (gradientDrawable == null) {  
            gradientDrawable = new GradientDrawable();  
            }  
            gradientDrawable.setColor(backColorSelectedi);  
        } else {  
            setBackgroundColor(backColorSelectedi);  
        }  
        } else if (!TextUtils.isEmpty(backColorSelecteds)) {  
        if (fillet) {  
            if (gradientDrawable == null) {  
            gradientDrawable = new GradientDrawable();  
            }  
            gradientDrawable.setColor(Color.parseColor(backColorSelecteds));  
        } else {  
            setBackgroundColor(Color.parseColor(backColorSelecteds));  
        }  
        }  
        // 判断是否设置了按下后文字的颜色  
        if (0 != textColorSeletedi) {  
        setTextColor(textColorSeletedi);  
        } else if (!textColorSeleteds.equals("")) {  
        setTextColor(Color.parseColor(textColorSeleteds));  
        }  
        // 判断是否设置了按下后的背景图  
        if (0 != backGroundImageSeleted) {  
        setBackgroundResource(backGroundImageSeleted);  
        }  
    }  
    if (event.getAction() == MotionEvent.ACTION_UP || event.getAction() == MotionEvent.ACTION_OUTSIDE  
        || event.getAction() == MotionEvent.ACTION_CANCEL) {  
        // 抬起  
        if (0 == backColori && TextUtils.isEmpty(backColors)) {  
        // 如果没有设置背景色，默认改为透明  
        if (fillet) {  
            if (gradientDrawable == null) {  
            gradientDrawable = new GradientDrawable();  
            }  
            gradientDrawable.setColor(Color.TRANSPARENT);  
        } else {  
            setBackgroundColor(Color.TRANSPARENT);  
        }  
        } else if (0 != backColori) {  
        if (fillet) {  
            if (gradientDrawable == null) {  
            gradientDrawable = new GradientDrawable();  
            }  
            gradientDrawable.setColor(backColori);  
        } else {  
            setBackgroundColor(backColori);  
        }  
        } else {  
        if (fillet) {  
            if (gradientDrawable == null) {  
            gradientDrawable = new GradientDrawable();  
            }  
            gradientDrawable.setColor(Color.parseColor(backColors));  
        } else {  
            setBackgroundColor(Color.parseColor(backColors));  
        }  
        }  
        // 如果为设置字体颜色，默认为白色  
        if (0 == textColori && TextUtils.isEmpty(textColors)) {  
        setTextColor(Color.WHITE);  
        } else if (textColori != 0) {  
        setTextColor(textColori);  
        } else {  
        setTextColor(Color.parseColor(textColors));  
        }  
        if (backGroundImage != 0) {  
        setBackgroundResource(backGroundImage);  
        }  
    }  
    return arg0.onTouchEvent(event);  
  
    }  
  
    /** 
     * 设置按钮的背景色,如果未设置则默认为透明 
     *  
     * @param backColor 
     */  
    public void setBackColor(String backColor) {  
    this.backColors = backColor;  
    if (backColor.equals("")) {  
        if (fillet) {  
        if (gradientDrawable == null) {  
            gradientDrawable = new GradientDrawable();  
        }  
        gradientDrawable.setColor(Color.TRANSPARENT);  
        } else {  
        setBackgroundColor(Color.TRANSPARENT);  
        }  
    } else {  
        if (fillet) {  
        if (gradientDrawable == null) {  
            gradientDrawable = new GradientDrawable();  
        }  
        gradientDrawable.setColor(Color.parseColor(backColor));  
        } else {  
        setBackgroundColor(Color.parseColor(backColor));  
        }  
    }  
    }  
  
    /** 
     * 设置按钮的背景色,如果未设置则默认为透明 
     *  
     * @param backColor 
     */  
    public void setBackColor(int backColor) {  
    this.backColori = backColor;  
    if (backColori == 0) {  
        if (fillet) {  
        if (gradientDrawable == null) {  
            gradientDrawable = new GradientDrawable();  
        }  
        gradientDrawable.setColor(Color.TRANSPARENT);  
        } else {  
        setBackgroundColor(Color.TRANSPARENT);  
        }  
    } else {  
        if (fillet) {  
        if (gradientDrawable == null) {  
            gradientDrawable = new GradientDrawable();  
        }  
        gradientDrawable.setColor(backColor);  
        } else {  
        setBackgroundColor(backColor);  
        }  
    }  
    }  
  
    /** 
     * 设置按钮按下后的颜色 
     *  
     * @param backColorSelected 
     */  
    public void setBackColorSelected(int backColorSelected) {  
    this.backColorSelectedi = backColorSelected;  
    }  
  
    /** 
     * 设置按钮按下后的颜色 
     *  
     * @param backColorSelected 
     */  
    public void setBackColorSelected(String backColorSelected) {  
    this.backColorSelecteds = backColorSelected;  
    }  
  
    /** 
     * 设置按钮的背景图 
     *  
     * @param backGroundImage 
     */  
    public void setBackGroundImage(int backGroundImage) {  
    this.backGroundImage = backGroundImage;  
    if (backGroundImage != 0) {  
        setBackgroundResource(backGroundImage);  
    }  
    }  
  
    /** 
     * 设置按钮按下的背景图 
     *  
     * @param backGroundImageSeleted 
     */  
    public void setBackGroundImageSeleted(int backGroundImageSeleted) {  
    this.backGroundImageSeleted = backGroundImageSeleted;  
    }  
  
    /** 
     * 设置按钮圆角半径大小 
     *  
     * @param radius 
     */  
    public void setRadius(float radius) {  
    if (gradientDrawable == null) {  
        gradientDrawable = new GradientDrawable();  
    }  
    gradientDrawable.setCornerRadius(radius);  
    }  
  
    /** 
     * 设置按钮文字颜色 
     *  
     * @param textColor 
     */  
    public void setTextColors(String textColor) {  
    this.textColors = textColor;  
    setTextColor(Color.parseColor(textColor));  
    }  
  
    /** 
     * 设置按钮文字颜色 
     *  
     * @param textColor 
     */  
    public void setTextColori(int textColor) {  
    this.textColori = textColor;  
    setTextColor(textColor);  
    }  
  
    /** 
     * 设置按钮按下的文字颜色 
     *  
     * @param textColor 
     */  
    public void setTextColorSelected(String textColor) {  
    this.textColorSeleteds = textColor;  
    }  
  
    /** 
     * 设置按钮按下的文字颜色 
     *  
     * @param textColor 
     */  
    public void setTextColorSelected(int textColor) {  
    this.textColorSeletedi = textColor;  
    }  
  
    /** 
     * 按钮的形状 
     *  
     * @param shape 
     */  
    public void setShape(int shape) {  
    this.shape = shape;  
    }  
  
    /** 
     * 设置其是否为圆角 
     *  
     * @param fillet 
     */  
    @SuppressWarnings("deprecation")  
    public void setFillet(Boolean fillet) {  
    this.fillet = fillet;  
    if (fillet) {  
        if (gradientDrawable == null) {  
        gradientDrawable = new GradientDrawable();  
        }  
        // GradientDrawable.RECTANGLE  
        gradientDrawable.setShape(shape);  
        gradientDrawable.setCornerRadius(radius);  
        setBackgroundDrawable(gradientDrawable);  
    }  
    }  
  
    class TimeLastUtil extends CountDownTimer {  
  
    /** 
     *  
     * @param millisInFuture 
     *            总的时间 
     * @param countDownInterval 
     *            计数间隔 
     * @param view 
     *            TextView或者Button 
     * @param prompt 
     *            自定义提示文字（默认"s后可重新发送"） 
     */  
    public TimeLastUtil(long millisInFuture, long countDownInterval, TextView view, String prompt) {  
        super(millisInFuture, countDownInterval);  
    }  
  
    @SuppressLint("NewApi")  
    @Override  
    public void onFinish() {  
        setText("获取验证码");  
        setClickable(true);// // 重新获得点击  
        setBackColor(Color.parseColor("#00CCCC"));// 还原背景色  
        timeIsEnd = true;  
    }  
  
    @Override  
    public void onTick(long millisUntilFinished) {  
        setClickable(false);// 设置不能点击  
        setText(millisUntilFinished / 1000 + "s后可重新发送");// 设置倒计时时间  
        // 设置按钮为灰色，这时是不能点击的  
        setBackColor(Color.GRAY);  
        Spannable span = new SpannableString(getText().toString());// 获取按钮的文字  
        span.setSpan(new ForegroundColorSpan(Color.RED), 0, String.valueOf(millisUntilFinished / 1000).length(),  
            Spannable.SPAN_INCLUSIVE_EXCLUSIVE);// 讲倒计时时间显示为红色  
        setText(span);  
    }  
  
    }  
}  
attrs
[html] view plain copy
<declare-styleable name="CustomButton">  
  
    <!-- 按钮点击效果 （color） -->  
    <attr name="clickeffect" format="color" />  
    <!-- 文字点击效果 （color） -->  
    <attr name="textclickeffect" format="color" />  
    <!-- 按钮背景（图片，color） -->  
    <attr name="customBackground" format="color" />  
    <!-- 文字颜色 （color） -->  
    <attr name="customTextColor" format="color" />  
    <!-- 是否为圆角 true：是，false：否 -->  
    <attr name="fillet" format="boolean" />  
    <!-- 圆角的角度（float） -->  
    <attr name="customRadius" format="float" />  
    <!-- 是否开启计时器 true：是，false：否 -->  
    <attr name="isStartTimer" format="boolean" />  
</declare-styleable>  
《还有些许功能并不完善，后期会抽空写好上传》
源码下载地址
http://download.csdn.net/detail/qq569699973/9506636
