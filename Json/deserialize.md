        /// <summary>
        /// 反序列化时间字符串为DateTime
        /// 0、序列化时，字符串会包括引号
        /// 1、时间字符换需要加引号
        /// 2、反序列化不要带timeFormat
        /// </summary>
        /// <param name="timeStr"></param>
        /// <param name="time"></param>
        /// <returns></returns>
        public DateTime Serialize(string timeStr,DateTime time)
        {
            Newtonsoft.Json.Converters.IsoDateTimeConverter timeFormat = new Newtonsoft.Json.Converters.IsoDateTimeConverter();
            timeFormat.DateTimeFormat = "yyyy-MM-dd HH:mm:ss.fff";

            string json = timeStr;
            DateTime data = DateTime.Now;
            try
            {
                //2、时间序列化--字符串多出来引号:"\"2018-04-02 00:00:00.000\""--时间字符串多出来引号
                var timeJson = Newtonsoft.Json.JsonConvert.SerializeObject(time, timeFormat);
                timeJson=Newtonsoft.Json.JsonConvert.SerializeObject(time, new Newtonsoft.Json.JsonConverter[] { timeFormat });

                //2、时间序列化--Newtonsoft.Json.Formatting.Indented(缩进)
                timeJson = Newtonsoft.Json.JsonConvert.SerializeObject(time, Newtonsoft.Json.Formatting.Indented, timeFormat);

                //1、时间反序列化--"2018-04-02 00:00:00.000"直接反序列化失败--反序列化失败
                //json = "2018-04-02";
                //data = Newtonsoft.Json.JsonConvert.DeserializeObject<DateTime>(json, timeFormat);//--失败
                //json = "2018-04-02 00:00:00.000";
                //data = Newtonsoft.Json.JsonConvert.DeserializeObject<DateTime>(json, timeFormat);//--失败

                //1、时间反序列化--字符串严格格式+加引号，后再反序列化"\"2018-04-02 00:00:00.000\""--反序列化成功
                //json = "2018-04-02";//"\"2018-04-02\""--失败
                //json = "2018-04-02 00:00:00.000";//"\"2018-04-02 00:00:00.000\""--成功
                //json = "\"" + json + "\""; //"\"2018-04-02\""--失败//"\"2018-04-02 00:00:00.000\""--成功
                //data = Newtonsoft.Json.JsonConvert.DeserializeObject<DateTime>(json, timeFormat);
                //data = (DateTime)Newtonsoft.Json.JsonConvert.DeserializeObject(json, typeof(DateTime), timeFormat);

                //1、反序列化不带timeFormat--成功
                json = "2018-04-02";//"\"2018-04-02\""--成功
                //json = "2018-04-02 00:00:00.000";//"\"2018-04-02 00:00:00.000\""--成功
                json = "\"" + json + "\""; //"\"2018-04-02\""--成功//"\"2018-04-02 00:00:00.000\""--成功
                //data = Newtonsoft.Json.JsonConvert.DeserializeObject<DateTime>(json);
                data = (DateTime)Newtonsoft.Json.JsonConvert.DeserializeObject(json, typeof(DateTime));
                var d = Newtonsoft.Json.JsonConvert.DeserializeObject<object>(json);

                //3、Action反射框架方法调用的参数处理方式
                json = "2018-04-02";
                Type type = typeof(DateTime);
                if (type.IsValueType)
                {
                    var obj = Convert.ChangeType(json, type);
                    data = (DateTime)obj;
                }
                else
                {
                    data = (DateTime)Newtonsoft.Json.JsonConvert.DeserializeObject(json, typeof(DateTime));
                }

            }
            catch (Exception)
            {

            }
            return data;
        }
        