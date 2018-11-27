## java-excel-utils
    是JAVA操作Excel导入导出的工具类，目的是简化逻辑操作、可拓展Excel导入导出配置。         
   (教程博客)[https://blog.csdn.net/JavaWebRookie/article/details/80843653]
   
   
### 导出配置 ExcelUtils.exportForExcel(...)
      
    * 可提供模板下载           
    * 自定义下拉列表：对每个单元格自定义下拉列表         
    * 自定义列宽：对每个单元格自定义列宽         
    * 自定义样式：对每个单元格自定义样式  
    * 自定义样式：单元格自定义某一列或者某一行样式            
    * 自定义单元格合并：对每个单元格合并 
    * 自定义：每个表格的大标题          
    * 自定义：对每个单元格固定表头          
 
### 导入配置 ExcelUtils.importForExcelData(...)
    * 获取多单元数据         
    * 自定义：多单元从第几行开始获取数据            
    * 自定义：多单元根据那些列为空来忽略行数据         


### maven使用

    <dependency>        
        <groupId>com.github.andyczy</groupId>       
        <artifactId>java-excel-utils</artifactId>       
        <version>2.0</version>      
    </dependency>   
        
### 数据格式
   导出数据：参数 dataLists
   
        @Override
           public List<List<String[]>> exportBill(String deviceNo,String snExt,Integer parentInstId,String startDate, String endDate){
               List<List<String[]>> listArray = new ArrayList<>();
               List<String[]> stringList = new ArrayList<>();
               PageInfo<BillInfo> pagePageInfo = getBillPage(1,10000,null,snExt,deviceNo,parentInstId,startDate,endDate);
               String[] valueString = null;
               String[] headers = {"序号","标题一","标题二","标题三","标题四","标题五","标题六"};
               stringList.add(headers);
               for (int i = 0; i < pagePageInfo.getList().size(); i++) {
                   valueString = new String[]{(i + 1) + "", pagePageInfo.getList().get(i).getSnExt(),
                           getNeededDateStyle(pagePageInfo.getList().get(i).getPayTime(),"yyyy-MM-dd hh:mm:ss"),
                           pagePageInfo.getList().get(i).getInstName(),pagePageInfo.getList().get(i).getStatisticsPrice()+"",
                           pagePageInfo.getList().get(i).getDeviceNo(),
                           pagePageInfo.getList().get(i).getWarning()==1?"是":"否"};
                   stringList.add(valueString);
               }
               listArray.add(stringList);
               return listArray;
           }       
   
   自定义列宽：参数 columnMap
   
       参数说明：
       HashMap<Integer, HashMap<Integer, Integer>> mapColumnWidth = new HashMap<>();
       HashMap<Integer, Integer> mapColumn = new HashMap<>();
       //自定义列宽
       mapColumn.put(0, 3);  //第一列、宽度为3
       mapColumn.put(1, 20);
       mapColumn.put(2, 15);
       //第一个单元格列宽
       mapColumnWidth.put(1, mapColumn);
       
   自定义固定表头：参数 paneMap
   
       参数说明：
       HashMap setPaneMap = new HashMap();
       //第一个表格、第三行开始固定表头
       setPaneMap.put(1, 1); 
       
   
   自定义合并单元格：参数 regionMap
   
        参数说明：
        List<List<Integer[]>> setMergedRegion = new ArrayList<>();
        List<Integer[]> sheet1 = new ArrayList<>();                  //第一个表格设置。
        Integer[] sheetColumn1 = new Integer[]{0, 1, 0, 2}         //代表起始行号，终止行号， 起始列号，终止列号进行合并。（excel从零行开始数）
        setMergedRegion.add(sheet1);
       
   自定义下拉列表值：参数 dropDownMap
   
        参数说明：
        HashMap hashMap = new HashMap();
        List<String[]> sheet1 = new ArrayList<>();                  //第一个表格设置。
        String[] sheetColumn1 = new String[]{"1", "2", "4"};        //必须放第一：设置下拉列表的列（excel从零行开始数）
        String[] sex = {"男,女"};                                   //下拉的值放在 sheetColumn1 后面。
        sheet1.add(sheetColumn1);
        sheet1.add(sex);
        hashMap.put(1,sheet1);                                      //第一个表格的下拉列表值   
        
   自定义每个表格第几行或者是第几列的样式：参数 rowStyles / columnStyles
           
        参数说明：
        HashMap hashMap = new HashMap();
        List list = new ArrayList();
        list.add(new Boolean[]{true, false, false, false, true});                //1、样式
        list.add(new Integer[]{1, 3});                                           //2、第几行或者是第几列
        list.add(new Integer[]{10,14,null});                                     //3、颜色值（8是黑色、10红色等） 、字体大小、行高（可不设置）
        hashMap.put(1,list);                                                     //第一表格
        
   自定义每一个单元格样式：参数 styles
        
       参数说明：
       HashMap cellStyles = new HashMap();
       List< List<Object[]>> list = new ArrayList<>();
       List<Object[]> objectsList = new ArrayList<>();
       List<Object[]> objectsListTwo = new ArrayList<>();
       objectsList.add(new Boolean[]{true, false, false, false, true});      //1、样式一（必须放第一）
       objectsList.add(new Integer[]{10, 12});                               //1、颜色值 、字体大小、行高（必须放第二）
       objectsList.add(new Integer[]{5, 1});                                 //1、第五行第一列
       objectsList.add(new Integer[]{6, 1});                                 //1、第六行第一列
       
       objectsListTwo.add(new Boolean[]{false, false, false, true, true});   //2、样式二（必须放第一）
       objectsListTwo.add(new Integer[]{10, 12,null});                       //2、颜色值 、字体大小、行高（必须放第二）
       objectsListTwo.add(new Integer[]{2, 1});                              //2、第二行第一列
       
       list.add(objectsList);
       list.add(objectsListTwo);
       cellStyles.put(1, list);                                              //第一个表格所有自定义单元格样式 
             
   
   
   导入配置：
        
       @param indexMap 多单元从第几行开始获取数据，默认从第二行开始获取（可为空，如 hashMapIndex.put(1,3); 第一个表格从第三行开始获取）
       @param continueRowMap 多单元根据那些列为空来忽略行数据（可为空，如 mapContinueRow.put(1,new Integer[]{1, 3}); 第一个表格从1、3列为空就忽略）
                    
### License
java-excel-utils is Open Source software released under the Apache 2.0 license.     