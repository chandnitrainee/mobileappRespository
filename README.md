/**
 * Login view model
 */

var app = app || {};

app.MarkUserEntry = (function () {
    'use strict';
    var $storeEmployeeId, $storeId, $breakEntryDescription, $breakEntryType;
    

    var storeInfoUserKendoDataSource = new kendo.data.DataSource({
        schema: {
            data: "d" //Specify how to get the result.
        },
        transport: {
            read: {
                url: app.helper.ETrackingWaveApiURL("getStoreEmployeeInfo"),
                type: "POST",
                contentType: "application/json; charset=utf-8",
                dataType: "json",
                crossDomain: true,
            },
            parameterMap: function () {
                return kendo.stringify({ masterAccountstoreId: kendo.toString($storeId), storeUserEmailId: kendo.toString($storeEmployeeId) });
            }
        },
        serverFiltering: true,
        change: function (e) {

        },
        
    });

    function setEntryButtons(lastEntryType) {
        alert(lastEntryType);
        if (lastEntryType == "BE") {
            MarkEntryViewModel.showBreakBeginEntryButton = false;
            MarkEntryViewModel.showBreakEndEntryButton = true;
            MarkEntryViewModel.showDayBeginEntryButton = false;
            MarkEntryViewModel.showDayEndEntryButton = true;
        //    document.getElementById("btnDayBegin").("class", "hideEntryButtons");
        //    document.getElementById("btnBreakBegin").setAttribute("class", "hideEntryButtons");
        //    document.getElementById("btnBreakEnd").setAttribute("class", "showEntryButtons");
        //    document.getElementById("btnDayEnd").setAttribute("class", "hideEntryButtons");
        }
        else if (lastEntryType == "DB") {
            MarkEntryViewModel.showBreakBeginEntryButton = true;
            MarkEntryViewModel.showBreakEndEntryButton = false;
            MarkEntryViewModel.showDayBeginEntryButton = true;
            MarkEntryViewModel.showDayEndEntryButton = true;
            //document.getElementById("btnDayBegin").setAttribute("class", "showEntryButtons");
            //document.getElementById("btnBreakBegin").setAttribute("class", "hideEntryButtons");
            //document.getElementById("btnBreakEnd").setAttribute("class", "hideEntryButtons");
            //document.getElementById("btnDayEnd").setAttribute("class", "hideEntryButtons");
        }
        else if (lastEntryType == "DE") {
            MarkEntryViewModel.showBreakBeginEntryButton = false;
            MarkEntryViewModel.showBreakEndEntryButton = false;
            MarkEntryViewModel.showDayBeginEntryButton = false;
            MarkEntryViewModel.showDayEndEntryButton = false;
            //document.getElementById("btnDayBegin").setAttribute("class", "hideEntryButtons");
            //document.getElementById("btnBreakBegin").setAttribute("class", "hideEntryButtons");
            //document.getElementById("btnBreakEnd").setAttribute("class", "hideEntryButtons");
            //document.getElementById("btnDayEnd").setAttribute("class", "hideEntryButtons");
        }
        else if (lastEntryType == "BEnd") {
            MarkEntryViewModel.showBreakBeginEntryButton = true;
            MarkEntryViewModel.showBreakEndEntryButton = false;
            MarkEntryViewModel.showDayBeginEntryButton = false;
            MarkEntryViewModel.showDayEndEntryButton = true;
            //document.getElementById("btnDayBegin").setAttribute("class", "hideEntryButtons");
            //document.getElementById("btnBreakBegin").setAttribute("class", "showEntryButtons");
            //document.getElementById("btnBreakEnd").setAttribute("class", "hideEntryButtons");
            //document.getElementById("btnDayEnd").setAttribute("class", "showEntryButtons");
        }
    }

    var MarkEntryViewModel = (function () {

        var showDayBeginEntryButton = true;
        var showDayEndEntryButton = true;
        var showBreakBeginEntryButton = true;
        var showBreakEndEntryButton = true;
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
            var p = e.view.params;
            $storeEmployeeId = p.storeEmployeeId;
            var masterAccountObjInfo = JSON.parse(app.helper.readObjectFromLocalStorage("accountStoreInfoDatasource"));
            $storeId = masterAccountObjInfo.storeId;
            onGetCurrentDayStoreUserEntry();
            //var datasourcedata = storeInfoUserKendoDataSource.read();
            ////alert(datasourcedata.length);

           
            //storeInfoUserKendoDataSource.fetch();
            //var datasourcedata = storeInfoUserKendoDataSource.data()

            //for (var i = 0; i < datasourcedata.length; i++) {
            //    var dataitem = datasourcedata[i].user_fullname;
            //    alert(dataitem);
            //}
          //  console.log(storeInfoUserKendoDataSource.data.toString());
          //  localStorage.setItem('speakerData', storeInfoUserKendoDataSource.data);
          //  app.helper.writeObjectIntoLocalStorage("aaa", storeInfoUserKendoDataSource.data);
        };
        var onGetCurrentDayStoreUserEntry = function () {
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
                        app.mobileApp.hideLoading();
                        if (result != null && result.d != null) {
                            lastEntryType = result.d[0].EntryType
                        }
                        else
                        {
                            lastEntryType = "BD";
                        }
                        setEntryButtons(lastEntryType);

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
        var show = function () {

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
            showDayBeginEntryButton :showDayBeginEntryButton,
            showDayEndEntryButton :showDayEndEntryButton,
            showBreakBeginEntryButton :showBreakBeginEntryButton,
            showBreakEndEntryButton :showBreakEndEntryButton
        };

    }());

    return MarkEntryViewModel;

}());
<div data-role="view" data-title="ETracking Wave" data-layout="ETrackingWaveHomeLayout" data-model="app.MarkUserEntry" data-init="app.MarkUserEntry.init" data-show="app.MarkUserEntry.show">
    <!--<header data-role="header">
        <div data-role="navbar">
           ETracking Wave - Mark Your Entries
           <a class="nav-button" data-align="left" data-role="backbutton" data-click="app.accountDashboard.logout">Back</a>
       </div>
  </header>-->
    <div class="controller-login">
        <div class="login-logo wagebase"></div>
        <form>
            <div class="login-holder">
                <div class="login-box">
                    <div class="card-section">
                        <label class="lblHeading" id="lblHeading">Make your Entry</label><br />
                        <label class="lblHeading" id="lblDateTime"></label>
                        <div class="form-joined">
                            <div class="form-item icon">
                                <div class="form-text large">
                                    <div data-bind="visible: showDayBeginEntryButton">
                                        <a id="btnDayBegin" data-role="button" class="button-kit xlarge green" data-bind="click: onMarkDayBeginEntry">Day Begin</a>
                                    </div>
                                </div>
                            </div>
                            <div class="form-item icon">
                                <div class="form-text large">
                                    <div data-bind="visible: showBreakBeginEntryButton">
                                        <a id="btnBreakBegin" data-role="button"  href="#replyBreakEntryOptions" data-rel="actionsheet" class="button-kit xlarge green">Break</a>
                                    </div>
                                    <div data-bind="visible: showBreakEndEntryButton">
                                        <a id="btnBreakEnd" data-role="button" data-bind="click: onMarkBreakEndEntry" class="hideEntryButtons button-kit xlarge green">Break End</a>
                                    </div>
                                </div>
                                <div class="form-text large">
                                    <ul data-role="actionsheet" id="replyBreakEntryOptions" data-cancel="Close">
                                        <li><a data-action="app.MarkUserEntry.onBreakLunchEntryType">Lunch</a></li>
                                        <li><a data-action="app.MarkUserEntry.onBreakPersonalEntryType">Personal</a></li>
                                        <li><a data-action="app.MarkUserEntry.onBreakOfficeEntryType">Office Work</a></li>
                                    </ul>
                                </div>
                            </div>


                            <div class="form-item icon">
                                <div class="form-text large">
                                    <div data-bind="visible: showDayEndEntryButton">
                                        <a id="btnDayEnd" data-role="button" class="button-kit xlarge green" data-bind="click: onMarkEndDayEntry">End of Day</a>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </form>
    </div>

</div>

