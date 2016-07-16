####1.获取运营商及网络信息
```

  public static String getCarrierName(Context context){
    String operatorsName;
    TelephonyManager manager=(TelephonyManager)context.getSystemService(Context.TELEPHONY_SERVICE);
    if(manager!=null){
      String subscriberId=manager.getSubscriberId();
      if(TextUtils.isEmpty(subscriberId)){
        operatorsName="NONE";
        return operatorsName;
      }else if(subscriberId.startsWith("46000")||subscriberId.startsWith("46002")||subscriberId.startsWith("46007")||subscriberId.startsWith("46020")){
        operatorsName="中国移动";
      }else if(subscriberId.startsWith("46001")||subscriberId.startsWith("46006")){
        operatorsName="中国联通";
      }else if(subscriberId.startsWith("46003")||subscriberId.startsWith("46005")||subscriberId.startsWith("46011")){
        operatorsName="中国电信";
      }else{
        operatorsName="其他CELL";
      }
      if(!"其他CELL".equals(operatorsName)){
        operatorsName+=getSimpleCellTypeName(manager.getNetworkType());
      }
      return operatorsName;
    }
    return "UNKNOWN";
  }

  private static String getSimpleCellTypeName(int type){
    switch(type){
      case TelephonyManager.NETWORK_TYPE_GPRS:
      case TelephonyManager.NETWORK_TYPE_EDGE:
      case TelephonyManager.NETWORK_TYPE_CDMA:
      case TelephonyManager.NETWORK_TYPE_1xRTT:
      case TelephonyManager.NETWORK_TYPE_IDEN:
        //GSMHideApi
        case16:
        return "";
      case TelephonyManager.NETWORK_TYPE_UMTS:
      case TelephonyManager.NETWORK_TYPE_EVDO_0:
      case TelephonyManager.NETWORK_TYPE_EVDO_A:
      case TelephonyManager.NETWORK_TYPE_HSDPA:
      case TelephonyManager.NETWORK_TYPE_HSUPA:
      case TelephonyManager.NETWORK_TYPE_HSPA:
      case TelephonyManager.NETWORK_TYPE_EVDO_B:
      case TelephonyManager.NETWORK_TYPE_EHRPD:
      case TelephonyManager.NETWORK_TYPE_HSPAP:
        //TD-SCDMAHideApi
        case17:
        return "3G";
      case TelephonyManager.NETWORK_TYPE_LTE:
        return "4G";
      default:
        return "UNKNOWN";
    }
  }

```
####2.https证书校验
```
	httpsURLConnection.setSSLSocketFactory(getSSLContext().getSocketFactory());

  public SSLContext getSSLContext() {

    if (sslContext == null) {
      try {
        CertificateFactory cf = CertificateFactory.getInstance("X.509");
        InputStream caInput = appGlobal.appContext.getAssets().open(Configuration.CERTIFICATE);
        Certificate ca;
        try {
          ca = cf.generateCertificate(caInput);
        } finally {
          caInput.close();
        }
        String keyStoreType = KeyStore.getDefaultType();
        KeyStore keyStore = KeyStore.getInstance(keyStoreType);
        keyStore.load(null, null);
        keyStore.setCertificateEntry("ca", ca);

        String tmfAlgorithm = TrustManagerFactory.getDefaultAlgorithm();
        TrustManagerFactory tmf = TrustManagerFactory.getInstance(tmfAlgorithm);
        tmf.init(keyStore);

        sslContext = SSLContext.getInstance("TLS");
        sslContext.init(null, tmf.getTrustManagers(), null);
      } catch (Exception e) {
        Logger.getLogger().e("init SSLContext error");
        sslContext = null;
        e.printStackTrace();
      }
    }
    return sslContext;
  }
  ```
