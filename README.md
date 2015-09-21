# mobileappRespository
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using System.Xml;
using System.Reflection;
using Microsoft.Office.Interop.Excel;
public partial class ReadXML : System.Web.UI.Page
{
    public class userInfo{
        public string UserId { get; set; }
        public string RestaurantName { get; set; }
        public string DemoRunCount { get; set; }
        public string DemoName { get; set; }
        public string ClientURLAddress { get; set; }
        public string CustomerName { get; set; }
        public string PrimaryPhoneNo { get; set; }
        public string MobileNo { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string Region { get; set; }
        public string Regioncode { get; set; }
        public string Country { get; set; }
        public string CountryCode { get; set; }
        public string ZIP { get; set; }
        public string FaxNo { get; set; }
        public string UserEmail { get; set; }
        public string DateTime { get; set; }
    }
    protected void Page_Load(object sender, EventArgs e)
    {

    }
    protected void btnReadXML_Click(object sender, EventArgs e)
    {
        XmlDocument xDoc = new XmlDocument();
        xDoc.Load(HttpContext.Current.Server.MapPath("~/data/userinfo.xml"));

        List<userInfo> userInfoCollection = new List<userInfo>();
        XmlNodeList xmlUserNodes = xDoc.SelectNodes("DemoUserInformations/User");

        foreach (XmlNode xmlNode in xmlUserNodes)
        {
            userInfo uinfo = new userInfo();
            if(xmlNode.Attributes["id"]!=null)
            uinfo.UserId = xmlNode.Attributes["id"].Value;
            if (xmlNode.Attributes["RName"] != null)
                uinfo.RestaurantName = xmlNode.Attributes["RName"].Value;
            if (xmlNode.Attributes["Demoruncount"] != null)
                uinfo.DemoRunCount = xmlNode.Attributes["Demoruncount"].Value;
            if (xmlNode.Attributes["DemoName"] != null)
                uinfo.DemoName = xmlNode.Attributes["DemoName"].Value;
            if(xmlNode.SelectSingleNode("ClientURLAddress")!=null)
             uinfo.ClientURLAddress =xmlNode.SelectSingleNode("ClientURLAddress").InnerText;
            if (xmlNode.SelectSingleNode("CName") != null)
                uinfo.CustomerName = xmlNode.SelectSingleNode("CName").InnerText;
            if (xmlNode.SelectSingleNode("Phno") != null)
                uinfo.PrimaryPhoneNo = xmlNode.SelectSingleNode("Phno").InnerText;
            if (xmlNode.SelectSingleNode("Mobno") != null)
                uinfo.MobileNo = xmlNode.SelectSingleNode("Mobno").InnerText;

            if (xmlNode.SelectSingleNode("Address/Street") != null)
                uinfo.Street = xmlNode.SelectSingleNode("Address/Street").InnerText;
            if (xmlNode.SelectSingleNode("Address/City") != null)
                uinfo.City = xmlNode.SelectSingleNode("Address/City").InnerText;
            if (xmlNode.SelectSingleNode("Address/Region") != null)
            {
                uinfo.Region = xmlNode.SelectSingleNode("Address/Region").InnerText;
                if (xmlNode.SelectSingleNode("Address/Region").Attributes["code"] != null)
                {
                    uinfo.Regioncode = xmlNode.SelectSingleNode("Address/Region").Attributes["code"].Value;

                }
            }
            if (xmlNode.SelectSingleNode("Address/Country") != null)
            {
                uinfo.Country = xmlNode.SelectSingleNode("Address/Country").InnerText;
                if (xmlNode.SelectSingleNode("Address/Country").Attributes["code"] != null)
                {
                    uinfo.CountryCode = xmlNode.SelectSingleNode("Address/Country").Attributes["code"].Value;

                }
             }
            if (xmlNode.SelectSingleNode("Address/ZIP") != null)
                uinfo.ZIP = xmlNode.SelectSingleNode("Address/ZIP").InnerText;

            if (xmlNode.SelectSingleNode("FaxNo") != null)
                uinfo.FaxNo = xmlNode.SelectSingleNode("Address/ZIP").InnerText;
            if (xmlNode.SelectSingleNode("Email") != null)
                uinfo.UserEmail = xmlNode.SelectSingleNode("Email").InnerText;
            if (xmlNode.SelectSingleNode("DateTime") != null)
                uinfo.DateTime = xmlNode.SelectSingleNode("DateTime").InnerText;
            if (xmlNode.SelectSingleNode("Address/ZIP") != null)
                uinfo.ZIP = xmlNode.SelectSingleNode("Address/ZIP").InnerText;

            userInfoCollection.Add(uinfo);
        }
        
    }

    public void createExcelFile()
    {
         Microsoft.Office.Interop.Excel.Application xlApp = new Microsoft.Office.Interop.Excel.Application();

        if (xlApp == null)
        {
            Console.WriteLine("EXCEL could not be started. Check that your office installation and project references are correct.");
            return;
        }
        xlApp.Visible = true;

        Workbook wb = xlApp.Workbooks.Add(XlWBATemplate.xlWBATWorksheet);
        Worksheet ws = (Worksheet)wb.Worksheets[1];

        if (ws == null)
        {
            Console.WriteLine("Worksheet could not be created. Check that your office installation and project references are correct.");
        }

        // Select the Excel cells, in the range c1 to c7 in the worksheet.
        Range aRange = ws.get_Range("C1", "C7");

        if (aRange == null)
        {
            Console.WriteLine("Could not get a range. Check to be sure you have the correct versions of the office DLLs.");
        }

        // Fill the cells in the C1 to C7 range of the worksheet with the number 6.
        Object[] args = new Object[1];
        args[0] = 6;
        aRange.GetType().InvokeMember("Value", BindingFlags.SetProperty, null, aRange, args);
    
        // Change the cells in the C1 to C7 range of the worksheet to the number 8.
        aRange.Value2 = 8;
    
    }
}
EtrackingWave.cs
 [WebMethod]
    public  List<wt_storeCurrentDayEntries> getStoreUserCurrentDayLatestEntry(string masterAccountStoreId, string storeUserId)
    {
        List<wt_storeCurrentDayEntries> wt_storeEmployeeEntryList=new List<wt_storeCurrentDayEntries>();
        try
        {
            //  logger.InfoFormat("insertStoreUserCurrentDayEntry called with for user:{0} and storeid:{1}",storeUserId, masterAccountStoreId,);
            wt_storeEmployeeEntryList = EtrackingWaveHelper.getStoreUserCurrentDayLatestEntry(masterAccountStoreId, storeUserId);
        
        }
        catch (Exception ex)
        {
            // logger.Error(ex.Message);
        }
        return wt_storeEmployeeEntryList;

    }

EtrackingWaveHelper
 public static List<wt_storeCurrentDayEntries> getStoreUserCurrentDayLatestEntry(string masterAccountstoreId, string storeUserId)
    {

        string connectionSting = ConfigurationManager.ConnectionStrings["VrindiAttendance"].ConnectionString;
        List<wt_storeCurrentDayEntries> wt_storeEmployeeEntryList = new List<wt_storeCurrentDayEntries>();
                 
        using (SqlConnection con = new SqlConnection(connectionSting))
        {
            using (SqlCommand cmd = new SqlCommand("sp_get_StoreUserCurrentDayEntry"))
            {
                cmd.CommandType = CommandType.StoredProcedure;
                cmd.Parameters.AddWithValue("@MasterAccountStoreID", new Guid(masterAccountstoreId));
                //cmd.Parameters.AddWithValue("@StoreUserId", new Guid(storeUserId));
                cmd.Parameters.AddWithValue("@StoreUserId", new Guid("0cc13e30-9e19-4d8a-8597-0a86a57d62b9"));

                cmd.Connection = con;
                con.Open();

                SqlDataReader idr = cmd.ExecuteReader();
                while (idr.Read())
                {
                    wt_storeCurrentDayEntries wt_storeEmployeeEntry = new wt_storeCurrentDayEntries();
                    if (idr["EntryType"] != DBNull.Value)
                    {
                        wt_storeEmployeeEntry.EntryType = Convert.ToString(idr["EntryType"]);
                    
                    if (idr["EntryDateTime"] != DBNull.Value)
                    {
                        wt_storeEmployeeEntry.EntryDateTime = Convert.ToDateTime(idr["EntryDateTime"]);
                    }
                     

                    wt_storeEmployeeEntryList.Add(wt_storeEmployeeEntry);
                }
               
            }
        }
            con.Close();
    }
        return wt_storeEmployeeEntryList;
    }

WT_storeEmployee.cs
 public class wt_storeCurrentDayEntries
    {
        public wt_storeCurrentDayEntries()
        {

        }
        public string EntryType { get; set; }
        public DateTime?  EntryDateTime { get; set; }
        


    }

in MarkEntries.js
in inti()  ,onGetCurrentDayStoreUserEntry();
after init();
  var onGetCurrentDayStoreUserEntry = function () {
            var ajaxURL = app.helper.ETrackingWaveApiURL("getStoreUserCurrentDayLatestEntry");
            try {
                $.ajax({
                    url: ajaxURL,
                    type: "POST",
                    contentType: "application/json; charset=utf-8",
                    dataType: "json",
                    crossDomain: true,
                    data: "{masterAccountStoreId:'" + $storeId + "',storeUserId:'" + $storeEmployeeId + "'}",
                    success: function (result) {
                        app.mobileApp.hideLoading();
                        alert(result.e);
                    },
                    error: function (xhr, ajaxOptions, thrownError) {
                        app.mobileApp.hideLoading();
                        app.showError("Please check your network connection.")
                    }
                });
            }
            catch (ex) {
                app.mobileApp.hideLoading();
                console.log(ex.toString());
            }
        };
     


in Return
 onGetCurrentDayStoreUserEntry: onGetCurrentDayStoreUserEntry


sp
-- =============================================
-- Author:		Pankaj
-- Create date: 2012-01-13
-- Description: Fetches latest consumer info from all the orders made.
-- =============================================
CREATE PROCEDURE [dbo].sp_get_StoreUserCurrentDayEntry
     @MasterAccountStoreID uniqueidentifier
	,@StoreUserId uniqueidentifier
AS
BEGIN
	-- SET NOCOUNT ON added to prevent extra result sets from interfering with SELECT statements.
	--SET NOCOUNT ON;
	
	Select entryType,entryDateTime from  wt_Store_User_Entry where Day(entryDateTime)=Day(GETDATE()) and MONTH(entryDateTime)=MONTH(GETDATE()) and Year(entryDateTime)=Year(GETDATE()) and store_id=@MasterAccountStoreID and user_id=@StoreUserId order by ins_upd_datetime desc,entryDateTime desc
END
