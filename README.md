/**
 * Login view model
 */

var app = app || {};

app.storeInfo = (function () {
    'use strict';
    //var ETrackingWaveApiURL = "http://etrackingwave.webondemo.com/ETrackingWaveApi/EtrackingWave.asmx/";
  //  var ETrackingWaveApiURL = "ETrackingWaveApi/EtrackingWave.asmx/";

    var storeInfoViewModel = (function () {

        var $loginStoreUsername, $loginStoreUserFullName, $loginStoreUserId, $loginStorePassword;
        var masterAccountStoreId;
          
        var init = function () {
            //var p = e.view.params;
           
            //$loginStoreUserFullName = p.suname;
            //$loginStoreUserId = p.suid;
            //$('#storeUserTitle').html("Hello <strong>" + $loginStoreUserFullName+"</strong>,Enter Password");
            //$loginStorePassword = $('#loginStorePassword');
            ////alert($loginStoreUserFullName);
            var masterAccountObjInfo = JSON.parse(app.helper.readObjectFromLocalStorage("accountStoreInfoDatasource"));
            masterAccountStoreId = masterAccountObjInfo.storeId;
            var isMasterAccountUserLoggedIn = app.helper.readFromLocalStorage("isMasterAccountUserLoggedIn");
            if (isMasterAccountUserLoggedIn === null || isMasterAccountUserLoggedIn != "true" || masterAccountStoreId === null || masterAccountObjInfo === null) {
                app.mobileApp.navigate("#ETrackingWaveHomeView");
            }
        };
        var login = function () {
            var password = $loginStorePassword.val();
            app.mobileApp.showLoading();
            if (password === "") {
                // navigator.notification.alert("Both fields are required!",
                //   function () { }, "Login failed", 'OK');
                alert("Password is Required");
                app.mobileApp.hideLoading();
                return;
            }
            var ajaxURL = app.helper.ETrackingWaveApiURL("checkStoreUserAccount");
            try {
                $.ajax({
                    url: ajaxURL,
                    type: "POST",
                    contentType: "application/json; charset=utf-8",
                    dataType: "json",
                    crossDomain: true,
                    data: "{storeUserId:'" + $loginStoreUserId + "',storeUserPassword:'" + password + "',masterAccountstoreId:'" + masterAccountStoreId + "'}",
                    success: function (result) {
                        if (result.d.toString() == "false") {
                            app.mobileApp.hideLoading();
                            alert("no user");
                        }
                        else if (result.d.toString() == "true") {
                            app.mobileApp.hideLoading();
                            alert("found user");
                            app.mobileApp.navigate('\#views/MarkEntries.html?suid=' + $loginStoreUserId + '&suname=' + $loginStoreUserFullName);
                        }
                    },
                    error: function () {
                        app.showError(err.message);
                    }
                });
            }
            catch (ex) {
                console.log(ex.toString());
            }
        };
        
        var show = function (e) {
            var p = e.view.params;
            $loginStorePassword = $('#loginStorePassword');
            $loginStorePassword.val('');
            $loginStoreUserFullName = p.suname;
            $loginStoreUserId = p.suid;
            $('#storeUserTitle').html("Hello <strong>" + $loginStoreUserFullName + "</strong>,Enter Password");
            

        };
        var logout = function () {

        };

        var markEntrees = function () {
            app.mobileApp.navigate('views/MarkEntries.html');
        }
        var viewStatus = function () {
            app.mobileApp.navigate('views/ViewStatus.html');
        }
        var createEmployeeAccount = function () {
            app.mobileApp.navigate('views/RegisterEmployees.html');
        }
        // Authenticate to use Backend Services as a particular user
     
        //console.log("datasource data:"+storeInfoUserKendoDataSource.data);
        //console.log(storeInfoUserKendoDataSource);
        //console.log("datasource data tot count:" + storeInfoUserKendoDataSource.data.length);
        return {
            init: init,
            show: show,
            markEntrees: markEntrees,
            viewStatus: viewStatus,
            createEmployeeAccount: createEmployeeAccount,
            logout: logout,
            login: login
        };

    }());

    return storeInfoViewModel;

}());
/**
 * MarkUserEntry View Model
 */

var app = app || {};

app.MarkUserEntry = (function () {
    'use strict';

    var $storeEmployeeId, $storeId, $breakEntryDescription, $breakEntryType, $loginStoreUsername;
    var $BreakBeginEntryButton, $BreakEndEntryButton, $DayBeginEntryButton, $DayEndEntryButton;
   
    function setEntryButtons(lastEntryType) {
        //alert(lastEntryType);
        if (lastEntryType == "NE") {
            $($DayBeginEntryButton).removeClass("hideEntryButtons");
            $($DayBeginEntryButton).addClass("showEntryButtons");

            $($DayEndEntryButton).removeClass("showEntryButtons");
            $($DayEndEntryButton).addClass("hideEntryButtons");

            $($BreakBeginEntryButton).removeClass("showEntryButtons");
            $($BreakBeginEntryButton).addClass("hideEntryButtons");

            $($BreakEndEntryButton).removeClass("showEntryButtons");
            $($BreakEndEntryButton).addClass("hideEntryButtons");
        }
       else if (lastEntryType == "BE") {
            $($DayBeginEntryButton).removeClass("showEntryButtons");
            $($DayBeginEntryButton).addClass("hideEntryButtons");

            $($DayEndEntryButton).removeClass("showEntryButtons");
            $($DayEndEntryButton).addClass("hideEntryButtons");

            $($BreakBeginEntryButton).removeClass("showEntryButtons");
            $($BreakBeginEntryButton).addClass("hideEntryButtons");

            $($BreakEndEntryButton).removeClass("hideEntryButtons");
            $($BreakEndEntryButton).addClass("showEntryButtons");
        }
        else if (lastEntryType == "DB") {
            $($DayBeginEntryButton).removeClass("showEntryButtons");
            $($DayBeginEntryButton).addClass("hideEntryButtons");

            $($DayEndEntryButton).removeClass("hideEntryButtons");
            $($DayEndEntryButton).addClass("showEntryButtons");

            $($BreakBeginEntryButton).removeClass("hideEntryButtons");
            $($BreakBeginEntryButton).addClass("showEntryButtons");

            $($BreakEndEntryButton).removeClass("showEntryButtons");
            $($BreakEndEntryButton).addClass("hideEntryButtons");           
        }
        else if (lastEntryType == "DE") {
            $($DayBeginEntryButton).removeClass("showEntryButtons");
            $($DayBeginEntryButton).addClass("hideEntryButtons");

            $($DayEndEntryButton).removeClass("showEntryButtons");
            $($DayEndEntryButton).addClass("hideEntryButtons");

            $($BreakBeginEntryButton).removeClass("showEntryButtons");
            $($BreakBeginEntryButton).addClass("hideEntryButtons");

            $($BreakEndEntryButton).removeClass("showEntryButtons");
            $($BreakEndEntryButton).addClass("hideEntryButtons");

            $('#lblHeading').html("<strong>" + $loginStoreUsername + "</strong>, Thankyou for Marking all your entries of today");
           
            
        }
        else if (lastEntryType == "BEnd") {
            $($DayBeginEntryButton).removeClass("showEntryButtons");
            $($DayBeginEntryButton).addClass("hideEntryButtons");

            $($DayEndEntryButton).removeClass("hideEntryButtons");
            $($DayEndEntryButton).addClass("showEntryButtons");

            $($BreakBeginEntryButton).removeClass("hideEntryButtons");
            $($BreakBeginEntryButton).addClass("showEntryButtons");

            $($BreakEndEntryButton).removeClass("showEntryButtons");
            $($BreakEndEntryButton).addClass("hideEntryButtons");
        }
    }

    var MarkEntryViewModel = (function () {
        var dsMyDatabase;
        
        var onBreakLunchEntryType = function () {
            $breakEntryType = "Lunch";
            onMarkBreakEntry();
        }
        var onBreakOfficeEntryType = function () {
            $breakEntryType = "Office Work";
            onMarkBreakEntry();
        }
        var onBreakPersonalEntryType = function () {
            $breakEntryType = "Personal";
            onMarkBreakEntry();
        }

        var init = function (e) {           
            //onGetCurrentDayStoreUserEntry();
        };
        var onGetCurrentDayStoreUserEntry = function () {
            app.mobileApp.showLoading();
            var lastEntryType;
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
                        
                        if (result != null && result.d != null && result.d[0] != null) {
                            lastEntryType = result.d[0].EntryType;
                            //dsMyDatabase = new kendo.data.DataSource.create({ data: result.d })
                            //$("#lmyEntries").kendoMobileListView({
                            //    dataSource: dsMyDatabase,
                            //    template: $("#myEntriesTemplate").text(),
                            //    style: "inset"
                            //});
                        }
                        else
                        {
                            lastEntryType = "NE";
                        }
                        setEntryButtons(lastEntryType);
                        app.mobileApp.hideLoading();
                    },
                    error: function (xhr, ajaxOptions, thrownError) {
                        app.mobileApp.hideLoading();
                        app.showError("Please check your network connection.")
                    }
                });
            }
            catch (ex) {
                app.mobileApp.hideLoading();
                app.showError("Please check your network connection.")
                console.log(ex.toString());
            }
        };
        var showUserEntryDetails = function () {
            alert("called");
            app.mobileApp.showLoading();
            var lastEntryType;
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

                        if (result != null && result.d != null && result.d[0] != null) {
                            lastEntryType = result.d[0].EntryType;
                            dsMyDatabase = new kendo.data.DataSource.create({ data: result.d })
                            $("#lmyEntries").kendoMobileListView({
                                dataSource: dsMyDatabase,
                                template: $("#myEntriesTemplate").text(),
                                style: "inset"
                            });
                        }
                        else {
                            lastEntryType = "NE";
                        }
                        //setEntryButtons(lastEntryType);
                        app.mobileApp.hideLoading();
                    },
                    error: function (xhr, ajaxOptions, thrownError) {
                        app.mobileApp.hideLoading();
                        app.showError("Please check your network connection.")
                    }
                });
            }
            catch (ex) {
                app.mobileApp.hideLoading();
                app.showError("Please check your network connection.")
                console.log(ex.toString());
            }
        };
        var onMarkDayBeginEntry = function () {
           app.mobileApp.showLoading();
           var ajaxURL = app.helper.ETrackingWaveApiURL("insertStoreUserCurrentDayEntry");
            try {
                $.ajax({
                    url: ajaxURL,
                    type: "POST",
                    contentType: "application/json; charset=utf-8",
                    dataType: "json",
                    crossDomain: true,
                    data: "{masterAccountStoreId:'" + $storeId + "',storeUserId:'" + $storeEmployeeId + "',entryType:'" + "DB" + "',entryDescription:'" + "DayBeginEntry" + "'}",
                    success: function (result) {
                        // onGetCurrentDayStoreUserEntry();
                        setEntryButtons("DB");
                        app.mobileApp.hideLoading();
                        alert("Entry Marked Successfully");
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
        var onMarkBreakEntry = function () {
            app.mobileApp.showLoading();
            var ajaxURL = app.helper.ETrackingWaveApiURL("insertStoreUserCurrentDayEntry");
            try {
                $.ajax({
                    url: ajaxURL,
                    type: "POST",
                    contentType: "application/json; charset=utf-8",
                    dataType: "json",
                    crossDomain: true,
                    data: "{masterAccountStoreId:'" + $storeId + "',storeUserId:'" + $storeEmployeeId + "',entryType:'" + "BE" + "',entryDescription:'" + $breakEntryType + "'}",
                    success: function (result) {
                        setEntryButtons("BE");
                       // onGetCurrentDayStoreUserEntry();
                        app.mobileApp.hideLoading();
                        alert("Entry Marked Successfully");
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
        var onMarkBreakEndEntry = function () {
            app.mobileApp.showLoading();
            var ajaxURL = app.helper.ETrackingWaveApiURL("insertStoreUserCurrentDayEntry");
            try {
                $.ajax({
                    url: ajaxURL,
                    type: "POST",
                    contentType: "application/json; charset=utf-8",
                    dataType: "json",
                    crossDomain: true,
                    data: "{masterAccountStoreId:'" + $storeId + "',storeUserId:'" + $storeEmployeeId + "',entryType:'" + "BEnd" + "',entryDescription:'" + $breakEntryType + "'}",
                    success: function (result) {
                        //onGetCurrentDayStoreUserEntry();
                        setEntryButtons("BEnd");
                        app.mobileApp.hideLoading();
                        alert("Entry Marked Successfully");
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
        var onMarkEndDayEntry = function () {
            app.mobileApp.showLoading();
            var ajaxURL = app.helper.ETrackingWaveApiURL("insertStoreUserCurrentDayEntry");
            try {
                $.ajax({
                    url: ajaxURL,
                    type: "POST",
                    contentType: "application/json; charset=utf-8",
                    dataType: "json",
                    crossDomain: true,
                    data: "{masterAccountStoreId:'" + $storeId + "',storeUserId:'" + $storeEmployeeId + "',entryType:'" + "DE" + "',entryDescription:'" + "DayEndEntry" + "'}",
                    success: function (result) {
                        // onGetCurrentDayStoreUserEntry();
                        setEntryButtons("DE");
                        app.mobileApp.hideLoading();
                        alert("Entry Marked Successfully");
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
        var onViewTodayEntries = function () {
            app.mobileApp.showLoading();
            var ajaxURL = app.helper.ETrackingWaveApiURL("insertStoreUserCurrentDayEntry");
            try {
                $.ajax({
                    url: ajaxURL,
                    type: "POST",
                    contentType: "application/json; charset=utf-8",
                    dataType: "json",
                    crossDomain: true,
                    data: "{masterAccountStoreId:'" + $storeId + "',storeUserId:'" + $storeEmployeeId + "',entryType:'" + "DE" + "',entryDescription:'" + "DayEndEntry" + "'}",
                    success: function (result) {
                        // onGetCurrentDayStoreUserEntry();
                        setEntryButtons("DE");
                        app.mobileApp.hideLoading();
                        alert("Entry Marked Successfully");
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
        var show = function (e) {
            $BreakBeginEntryButton, $BreakEndEntryButton, $DayBeginEntryButton, $DayEndEntryButton;
            $BreakEndEntryButton = $('#btnBreakEnd');
            $BreakBeginEntryButton = $('#btnBreakBegin');
            $DayBeginEntryButton = $('#btnDayBegin');
            $DayEndEntryButton = $('#btnDayEnd');
            var p = e.view.params;
            $storeEmployeeId = p.suid;
            $loginStoreUsername = p.suname;
            $('#lblHeading').html("<strong>" + $loginStoreUsername + "</strong>, Make your Entry");

            var masterAccountObjInfo = JSON.parse(app.helper.readObjectFromLocalStorage("accountStoreInfoDatasource"));
            $storeId = masterAccountObjInfo.storeId;
            onGetCurrentDayStoreUserEntry();
        };
        var logout = function () {

        };

      
        return {
            init: init,
            show: show,
            logout: logout,
            onMarkDayBeginEntry: onMarkDayBeginEntry,
            onMarkBreakEntry: onMarkBreakEntry,
            onMarkEndDayEntry: onMarkEndDayEntry,
            onBreakLunchEntryType: onBreakLunchEntryType,
            onBreakPersonalEntryType: onBreakPersonalEntryType,
            onBreakOfficeEntryType: onBreakOfficeEntryType,
            onGetCurrentDayStoreUserEntry: onGetCurrentDayStoreUserEntry,
            onMarkBreakEndEntry: onMarkBreakEndEntry,
            showUserEntryDetails: showUserEntryDetails
            
        };

    }());

    return MarkEntryViewModel;

}());

/**
 * Login view model
 */

var app = app || {};

app.storeUserAccount = (function () {
    'use strict';
    //  var ETrackingWaveApiURL = "http://etrackingwave.webondemo.com/ETrackingWaveApi/EtrackingWave.asmx/";
    var storeUserAccountViewModel = (function () {

        var $storeUserUserName, $storeUserPassword, $storeUserFullName;
        var masterAccountStoreId;
        var init = function () {
            $storeUserUserName = $('#storeUserUserName');
            $storeUserPassword = $('#storeUserPassword');
            $storeUserFullName = $('#storeUserFullName');
           
        };
      
        var show = function () {
            $storeUserUserName.val('');
            $storeUserPassword.val('');
            $storeUserFullName.val('');
           
        };

        // Authenticate to use Backend Services as a particular user
        var createStoreUserAccount = function () {
            var username = $storeUserUserName.val();
            var password = $storeUserPassword.val();
            var storeUserFullName = $storeUserFullName.val();
            app.mobileApp.showLoading();

            if (storeUserFullName === "") {
                app.mobileApp.hideLoading();
                // navigator.notification.alert("Please Employee Full Name",
                //   function () { }, "Employee Registration Failed", 'OK');
                alert("Please Employee Full Name");
                return;
            }
            if (username === "") {
                app.mobileApp.hideLoading();
                // navigator.notification.alert("Please enter a Email-Id",
                //   function () { }, ""Employee Registration Failed", 'OK');
                alert("Please enter a Email-Id");
                return;
            }
            var validator = $("#storeUserUserName").kendoValidator(
               {
                   messages: {
                       email: "",
                       //email: "Please enter a valid Email Address" uncomment this only when msg is to be displayed on form
                   }
               }
            ).data("kendoValidator");

            if (!validator.validate()) {
                app.mobileApp.hideLoading();
                // navigator.notification.alert("Please enter valid E-mail address",
                //   function () { }, "Login failed", 'OK');
                alert("Please enter valid E-mail address");
                return;
            }

            if (password === "") {
                app.mobileApp.hideLoading();
                // navigator.notification.alert("Please enter both username and password!",
                //   function () { }, "Employee Registration Failed", 'OK');
                alert("Please enter a password for your company account");
                return;
            }


            var masterAccountObjInfo = JSON.parse(app.helper.readObjectFromLocalStorage("accountStoreInfoDatasource"));
            masterAccountStoreId = masterAccountObjInfo.storeId;
           // alert(masterAccountStoreId)
            var isMasterAccountUserLoggedIn = app.helper.readFromLocalStorage("isMasterAccountUserLoggedIn");
            if (isMasterAccountUserLoggedIn === null || isMasterAccountUserLoggedIn != "true" || masterAccountStoreId === null || masterAccountObjInfo === null) {
                app.mobileApp.navigate("#ETrackingWaveHomeView");
            }
            else {
               
                try {
                   
                }
                catch (ex) {
                    app.mobileApp.hideLoading();
                    console.log(ex.toString());
                }
            }

          

                 

            //var ajaxURL = ETrackingWaveApiURL + "checkStoreAccount";
            var ajaxURL = app.helper.ETrackingWaveApiURL("checkStoreUserAccountExist");
            try {
                $.ajax({
                    url: ajaxURL,
                    type: "POST",
                    contentType: "application/json; charset=utf-8",
                    dataType: "json",
                    crossDomain: true,
                    //  data: "{storeUserEmailId:'" + username + "',storeUserPassword:'" + password + "',masterAccountstoreId:'" + masterAccountStoreId + "'}",
                    data: "{storeUserEmailId:'" + username + "',masterAccountstoreId:'" + masterAccountStoreId + "'}",

                    success: function (result) {
                        if (result.d.toString() == "true") {
                            app.mobileApp.hideLoading();
                            // navigator.notification.alert("User with same account already exist in your account",
                            //   function () { }, "Employee Registration Failed", 'OK
                            alert("User with same account already exist in your account");
                        }
                        else if (result.d.toString() == "false") {
                            app.mobileApp.hideLoading();
                            //alert("You can create account");
                            /**START of region for creating company account**/
                            var ajaxURL = app.helper.ETrackingWaveApiURL("createStoreUserAccount");
                            try {
                                $.ajax({
                                    url: ajaxURL,
                                    type: "POST",
                                    contentType: "application/json; charset=utf-8",
                                    dataType: "json",
                                    crossDomain: true,
                                    data: "{storeUserEmailId:'" + username + "',storeUserPassword:'" + password + "',storeUserFullName:'" + storeUserFullName + "',masterAccountstoreId:'" + masterAccountStoreId + "'}",
                                    success: function () {
                                        app.mobileApp.hideLoading();
                                        // navigator.notification.alert("Your Company Account created successfully..,Please check your registered inbox for details",
                                        //   function () { }, "Registered Successfully", 'OK
                                        alert("Employee Registered Successfully..,Please check your registered inbox for details");
                                        app.mobileApp.navigate('views/AccountDashboard.html');
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
                            /**END region **/
                        }
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

   return {
            init: init,
            show: show,
            getYear: app.getYear,
            createStoreUserAccount: createStoreUserAccount,
            
        };

    }());

    return storeUserAccountViewModel;

}());

