


包规范，使用MPV模式，根据类别区分  

![code1](https://github.com/DreamXLong/DreamXLong.github.io/blob/master/img/code1.png?raw=true)

一些自定义的工具类，比如时间工具类，Json解析工具类，Toast工具类等等  

![code2](https://github.com/DreamXLong/DreamXLong.github.io/blob/master/img/code2.png?raw=true)

一些自定义view    

![code2](https://github.com/DreamXLong/DreamXLong.github.io/blob/master/img/code3.png?raw=true)


···


Activity基类，包含一些公用方法，方便子类activity更加快捷的去实现方法，复用性强
public abstract class BaseActivity extends AppCompatActivity {

    public Context mContext;
    private int count;//记录开启进度条的情况 只能开一个
    private LayoutInflater inflater;
    private View titleView;//标题栏
    private int TITLE_ID = 1;
    private RelativeLayout rl_base;//最外层布局
    private RelativeLayout rootView;//内容布局
    private AutoRelativeLayout titleLayout;
    private AlertDialog loadingDialog;
    private TextView mTitleText;
    private ImageView mBackView;
    private ImageView mRightImg;
    private ImageView mRightImg2;
    private TextView mRightText;
    private View titleLine;
    private View mContentView;
    private View mEmptyView;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        doBeforeSetcontentView();
        inflater = LayoutInflater.from(this);
        titleView = inflater.inflate(R.layout.title, null);
        titleLayout = titleView.findViewById(R.id.rl_title);
        mTitleText = titleView.findViewById(R.id.tv_title_text);
        titleView.setId(TITLE_ID);
        mBackView = titleView.findViewById(R.id.iv_back);
        mBackView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                onBackPressed();
            }
        });
        mRightImg = titleView.findViewById(R.id.title_right_img);
        mRightImg2 = titleView.findViewById(R.id.title_right_img2);
        mRightText = titleView.findViewById(R.id.title_right_text);
        titleLine = titleView.findViewById(R.id.title_line);
        //最外层布局
        rl_base = new RelativeLayout(this);
//        rl_base.setBackgroundResource(R.color.color_common_bg);
        //内容布局
        rootView = new RelativeLayout(this);
        rootView.setPadding(0, 0, 0, 0);

        //填入titleView
        LinearLayout.LayoutParams lp = new LinearLayout.LayoutParams(LinearLayout.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.WRAP_CONTENT);
        rl_base.addView(titleView, lp);

        titleView.setVisibility(View.VISIBLE);

        //填入内容布局
        mContentView = inflater.inflate(initLayout(), null);
        rootView.addView(mContentView, new LinearLayout.LayoutParams(LinearLayout.LayoutParams.MATCH_PARENT, LinearLayout.LayoutParams.MATCH_PARENT));
        RelativeLayout.LayoutParams layoutParamsContent = new RelativeLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.WRAP_CONTENT);
        layoutParamsContent.addRule(RelativeLayout.BELOW, titleView.getId());
        rl_base.addView(rootView, layoutParamsContent);

        //设置ContentView
        setContentView(rl_base, new LinearLayout.LayoutParams(LinearLayout.LayoutParams.MATCH_PARENT, LinearLayout.LayoutParams.MATCH_PARENT));

        // 默认着色状态栏
        SetStatusBarColor(getResources().getColor(R.color.transparent_50));
        mContext = this;
        this.initView();
        this.initPresenter();

    }

    /**
     * 设置layout前配置
     */
    private void doBeforeSetcontentView() {
        // 把actvity放到application栈中管理
        AppManager.getAppManager().addActivity(this);
        // 无标题
        requestWindowFeature(Window.FEATURE_NO_TITLE);
        // 设置竖屏
        setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT);

    }

    /**
     * 设置内容布局
     */
    public abstract int initLayout();

    /**
     * 隐藏标题布局
     */
    public void hideTitleView() {
        titleView.setVisibility(View.GONE);
    }

    /**
     * 显示标题
     */
    public void showTitleView(String title) {
        titleView.setVisibility(View.VISIBLE);
        mTitleText.setText(title);
    }

    public TextView getTitleTextView() {
        titleView.setVisibility(View.VISIBLE);
        return mTitleText;
    }

    /**
     * 获取内容布局
     */
    public RelativeLayout getRootView() {
        return rootView;
    }

    public void setTitleTextColor(int color) {
        mTitleText.setTextColor(color);
    }

    public void setTitleLayoutColor(int color) {
        this.titleLayout.setBackgroundColor(color);
    }

    public void setTitleRightResource(int resId) {
        mRightImg.setImageResource(resId);
    }

    public void setTitleRightClick(View.OnClickListener clickListener) {
        mRightImg.setOnClickListener(clickListener);
    }

    public void setTitleRightResource2(int resId) {
        mRightImg2.setImageResource(resId);
    }

    public void setTitleRightClick2(View.OnClickListener clickListener) {
        mRightImg2.setOnClickListener(clickListener);
    }

    public ImageView getBackView() {
        return mBackView;
    }

    public void setTitleLeftColor(int color) {
        mBackView.setColorFilter(color);
    }

    public void setTitleRightText(String rightText) {
        mRightText.setVisibility(View.VISIBLE);
        mRightText.setText(rightText);
    }

    public void setTitleRightColor(int color) {
        mRightText.setTextColor(color);
    }

    public void setTitleRightTextClick(View.OnClickListener clickListener) {
        mRightText.setOnClickListener(clickListener);
    }

    public void setTitleLineVisible(int visible) {
        titleLine.setVisibility(visible);
    }

    /**
     * 显示加载等待Dialog
     */
    public void showLoadingDialog() {
        showLoadingDialog("请稍等");
    }

    /**
     * 显示加载等待Dialog
     *
     * @param loadingStr
     */
    public void showLoadingDialog(String loadingStr) {
        dismissDialog();
        AlertDialog.Builder builder = new AlertDialog.Builder(this, R.style.MyDialog);
        loadingDialog = builder.create();
        loadingDialog.show();
        LayoutInflater inflater = LayoutInflater.from(this);
        View view = inflater.inflate(R.layout.view_loading_dialog, null);
        TextView tipTextview = (TextView) view.findViewById(R.id.tipTextView);
        if (TextUtils.isEmpty(loadingStr)) {
            tipTextview.setVisibility(View.GONE);
        } else {
            tipTextview.setVisibility(View.VISIBLE);
            tipTextview.setText(loadingStr);
        }
        loadingDialog.setContentView(view);
        loadingDialog.setCanceledOnTouchOutside(false);
        Window dialogWindow = loadingDialog.getWindow();
        dialogWindow.setGravity(Gravity.CENTER);
        android.view.WindowManager.LayoutParams layoutParams = dialogWindow.getAttributes();
        layoutParams.height = getWindowManager().getDefaultDisplay().getWidth() * 1 / 3;
        layoutParams.width = getWindowManager().getDefaultDisplay().getWidth() * 1 / 3;
        dialogWindow.setAttributes(layoutParams);
        loadingDialog.show();

    }

    /**
     * 隐藏加载等待Dialog
     */
    public void dismissDialog() {
        if (loadingDialog != null) {
            try {
                loadingDialog.dismiss();
                loadingDialog = null;

            } catch (Exception e) {
            }
        }
    }

    /**
     * 显示内容View
     */
    public void showContentView() {
        if (rootView.getChildCount() > 0) {
            if (mContentView == rootView.getChildAt(0)) {
                return;
            }
        }
        rootView.removeAllViews();
        ViewUtil.removeSelfFromParent(mContentView);
        rootView.addView(mContentView, new LinearLayout.LayoutParams(
                ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT));
    }

    /**
     * 显示空View
     */
    public static final int EmptyType_NETWORK = 2003;
    public static final int EmptyType_SHOUCANG = 2004;
    public static final int EmptyType_SEARCH = 2005;
    public static final int EmptyType_BOOK = 2006;
    public static final int EmptyType_MSG = 2007;

    public void showEmptyView(int type) {
        if (rootView.getChildCount() > 0) {
            if (mEmptyView == rootView.getChildAt(0)) {
                return;
            }
        }
        rootView.removeAllViews();

        if (mEmptyView == null) {
            initEmptyView();
        }
        ImageView emptyImg = mEmptyView.findViewById(R.id.iv_empty);
        switch (type) {
            case EmptyType_NETWORK:
                emptyImg.setImageResource(R.drawable.meiyouwang);
                break;
            case EmptyType_BOOK:
                emptyImg.setImageResource(R.drawable.shujiakong);
                break;
            case EmptyType_SHOUCANG:
                emptyImg.setImageResource(R.drawable.shoucang);
                break;
            case EmptyType_SEARCH:
                emptyImg.setImageResource(R.drawable.wusousuo);
                break;
            case EmptyType_MSG:
                emptyImg.setImageResource(R.drawable.wuxiaoxi);
                break;
        }

        ViewUtil.removeSelfFromParent(mEmptyView);
        rootView.addView(mEmptyView, new LinearLayout.LayoutParams(
                ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT));
    }

    public void showEmptyView(int type, int width, int hight) {
        if (rootView.getChildCount() > 0) {
            if (mEmptyView == rootView.getChildAt(0)) {
                return;
            }
        }
        rootView.removeAllViews();

        if (mEmptyView == null) {
            initEmptyView();
        }
        ImageView emptyImg = mEmptyView.findViewById(R.id.iv_empty);
        emptyImg.setLayoutParams(new LinearLayout.LayoutParams(width, hight));
        switch (type) {
            case EmptyType_NETWORK:
                emptyImg.setImageResource(R.drawable.meiyouwang);
                break;
            case EmptyType_BOOK:
                emptyImg.setImageResource(R.drawable.shujiakong);
                break;
            case EmptyType_SHOUCANG:
                emptyImg.setImageResource(R.drawable.shoucang);
                break;
            case EmptyType_SEARCH:
                emptyImg.setImageResource(R.drawable.wusousuo);
                break;
            case EmptyType_MSG:
                emptyImg.setImageResource(R.drawable.wuxiaoxi);
                break;
        }
        ViewUtil.removeSelfFromParent(mEmptyView);
        rootView.addView(mEmptyView, new LinearLayout.LayoutParams(
                ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT));
    }

    public void initEmptyView() {
        mEmptyView = inflater.inflate(R.layout.view_empty, null);
    }

    /*********************
     * 子类实现
     *****************************/

    //简单页面无需mvp就不用管此方法即可,完美兼容各种实际场景的变通
    public abstract void initPresenter();

    //初始化view
    public abstract void initView();


    /**
     * 着色状态栏（4.4以上系统有效）
     */
    protected void SetStatusBarColor(int color) {
        StatusBarCompat.setStatusBarColor(this, color);
    }

    /**
     * 着色状态栏（4.4以上系统有效）
     */
    protected void SetStatusBarColor(int color, int alpha) {
        StatusBarCompat.setStatusBarColor(this, color, alpha);
    }

    /**
     * 沉浸状态栏（4.4以上系统有效）
     */
    protected void SetTranslanteBar() {
//        StatusBarSetting.setTranslucent(this);
        StatusBarCompat.translucentStatusBar(this, true);
    }


    @Override
    protected void onResume() {
        super.onResume();

    }

    @Override
    protected void onPause() {
        super.onPause();

    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions,
                                           @NonNull int[] grantResults) {
        XPermissionUtils.onRequestPermissionsResult(this, requestCode, permissions, grantResults);
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        AppManager.getAppManager().finishActivity(this);
    }
}

常用手机号格式类
public class PhoneUtils {

    /**
     * 获取常用手机信息
     *
     * @param context
     * @return
     */
    public static Map<String, String> getAppInfo(Context context) {
        Map<String, String> header = new HashMap<String, String>();
        //获取设备ID
        TelephonyManager tm = (TelephonyManager) context.getSystemService(Context.TELEPHONY_SERVICE);
        String deviceId = tm.getDeviceId();

        //获取手机操作系统版本号
        String appBuild = Build.VERSION.RELEASE;

        //获取手机品牌
        String brand = Build.BRAND;

        //获取应用版本号
        PackageInfo pi = null;
        PackageManager pm = context.getPackageManager();
        try {
            pi = pm.getPackageInfo(context.getPackageName(),
                    PackageManager.GET_CONFIGURATIONS);
        } catch (PackageManager.NameNotFoundException e) {
            e.printStackTrace();
        }
        String versionCode = String.valueOf(pi.versionCode);

        //获取应用版本名称
        String versionName = pi.versionName;

        //将各个信息加进集合
        header.put("deviceId", deviceId);
        header.put("appVersion", versionName);
        header.put("appBuild", versionCode);
        header.put("os", "android");
        header.put("osVersion", appBuild);
        header.put("brand", brand);

        //将数据存储起来
        SpUtil.putString("deviceId", deviceId);
        SpUtil.putString("appVersion", versionName);
        SpUtil.putString("appBuild", versionCode);
        SpUtil.putString("os", "android");
        SpUtil.putString("osVersion", appBuild);
        SpUtil.putString("brand", brand);
        SpUtil.putBoolean("has_header", true);

        return header;
    }

    /**
     * 判断手机号格式
     *
     * @param phoneNums 手机号
     * @return true 表示手机号格式正确   false 表示手机号格式错误
     */
    public static boolean judgePhoneNums(String phoneNums) {
        if (isMatchLength(phoneNums, 11)
                && isMobileNO(phoneNums)) {
            return true;
        }
        return false;
    }

    /**
     * 验证手机号的位数
     *
     * @param str
     * @param length
     * @return
     */
    public static boolean isMatchLength(String str, int length) {
        if (str.isEmpty()) {
            ToastUtil.show("请输入手机号码");
            return false;
        } else {
            if (str.length() != length) {
                ToastUtil.show("手机号码格式不正确");
            }
            return str.length() == length ? true : false;

        }
    }

    /**
     * 验证手机格式
     */
    public static boolean isMobileNO(String mobileNums) {
        /*
         * 移动：134、135、136、137、138、139、150、151、157(TD)、158、159、187、188
       * 联通：130、131、132、152、155、156、185、186 电信：133、153、180、189、（1349卫通）
       * 总结起来就是第一位必定为1，第二位必定为3或5或8，其他位置的可以为0-9
       */
        String telRegex = "[1][34578]\\d{9}";// "[1]"代表第1位为数字1，"[34578]"代表第二位可以为3、
        // 4、5、7、8中的一个，"\\d{9}"代表后面是可以是0～9的数字，有9位。
        if (TextUtils.isEmpty(mobileNums)) {
            ToastUtil.show("请输入手机号码");
            return false;
        } else {
            if (!mobileNums.matches(telRegex)) {
                ToastUtil.show("手机号码无效");
            }
            return mobileNums.matches(telRegex);
        }
    }

    /**
     * 检测验证码
     *
     * @param code
     * @return
     */
    public static boolean checkCode(String code) {
        if (TextUtils.isEmpty(code)) {
            ToastUtil.show("请输入验证码");
            return false;
        } else {
            return true;
        }
    }

    /**
     * 检测密码输入格式
     *
     * @param pwd
     * @return
     */
    public static boolean checkPwd(String pwd) {
        String str = "^[\\@A-Za-z0-9\\!\\#\\$\\%\\^\\&\\*\\.\\~]{6,100}$";
        Pattern p = Pattern.compile(str);
        Matcher m = p.matcher(pwd);
        return m.matches();
    }

    public static String getUniqueId(Context context) {
        String androidID = Settings.Secure.getString(context.getContentResolver(), Settings.Secure.ANDROID_ID);
        String id = androidID + Build.SERIAL;
        String encode = Md5Util.encode(id);
        return encode;
    }
}


封装的RecyclclerviewAdapter基类，方便使用Recyclerview时Adapter的使用，如点击事件，数据的写入
public abstract class BaseRecyclerAdapter<T> extends RecyclerView.Adapter<RecyclerView.ViewHolder> {
    protected List<T> mDatas = new ArrayList<>();
    private OnItemClickListener<T> mOnItemClickListener;
    private OnItemLongClickListener<T> mOnItemLongClickListener;
    protected Context mContext;

    /**
     * @param mDatas
     */
    public BaseRecyclerAdapter(List<T> mDatas) {
        this.mDatas = mDatas;
    }

    public void setData(List<T> mDatas) {
        this.mDatas = mDatas;
    }

    public void append(T item) {
        if (item != null) {
            mDatas.add(item);
            notifyDataSetChanged();
        }
    }

    public void append(List<T> datas) {
        if (datas != null && datas.size() > 0) {
            mDatas.addAll(datas);
            notifyDataSetChanged();
        }
    }


    public void replace(List<T> datas) {
        mDatas.clear();
        if (datas != null && datas.size() > 0) {
            mDatas.addAll(datas);
            notifyDataSetChanged();
        }
    }

    public void remove(int position) {
        mDatas.remove(position);
        notifyDataSetChanged();
    }

    public void removeAll() {
        mDatas.clear();
        notifyDataSetChanged();
    }

    public void setOnItemClickListener(OnItemClickListener<T> listener) {
        this.mOnItemClickListener = listener;
    }

    public void setOnItemLongClickListener(OnItemLongClickListener<T> listener) {
        this.mOnItemLongClickListener = listener;
    }

    public interface OnItemClickListener<T> {

        void onItemClick(View view, int position, T model);
    }

    public interface OnItemLongClickListener<T> {

        boolean onItemLongClick(View view, int position, T model);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public int getItemCount() {
        return mDatas == null ? 0 : mDatas.size();
    }

    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder, final int position) {
        onBindMyViewHolder(holder, position);
        initItemClickListener(holder, position);
    }

    private void initItemClickListener(RecyclerView.ViewHolder holder, final int position) {
        holder.itemView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mOnItemClickListener != null)
                    mOnItemClickListener.onItemClick(v, position, mDatas.get(position));
            }
        });
        if (mOnItemLongClickListener != null) {
            holder.itemView.setOnLongClickListener(new View.OnLongClickListener() {
                @Override
                public boolean onLongClick(View v) {
                    return mOnItemLongClickListener.onItemLongClick(v, position, mDatas.get(position));
                }
            });
        }
    }

    protected abstract void onBindMyViewHolder(RecyclerView.ViewHolder baseHolder, int position);

}




···