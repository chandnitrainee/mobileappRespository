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
