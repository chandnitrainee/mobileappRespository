/**
 * MarkUserEntry View Model
 */

var app = app || {};

app.MarkUserEntry = (function () {
    'use strict';

    var $storeEmployeeId, $storeId, $breakEntryDescription, $breakEntryType;
    var $BreakBeginEntryButton, $BreakEndEntryButton, $DayBeginEntryButton, $DayEndEntryButton;
   
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
            $BreakBeginEntryButton, $BreakEndEntryButton, $DayBeginEntryButton, $DayEndEntryButton;
            $BreakEndEntryButton = $('#btnBreakEnd');
            $BreakBeginEntryButton = $('#btnBreakBegin');
            $DayBeginEntryButton = $('#btnDayBegin');
            $DayEndEntryButton = $('#btnDayEnd');
            var p = e.view.params;
            $storeEmployeeId = p.storeEmployeeId;
            var masterAccountObjInfo = JSON.parse(app.helper.readObjectFromLocalStorage("accountStoreInfoDatasource"));
            $storeId = masterAccountObjInfo.storeId;
            onGetCurrentDayStoreUserEntry();
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
            showUserEntryDetails: showUserEntryDetails
            
        };

    }());

    return MarkEntryViewModel;

}());

<div data-role="view" data-title="ETracking Wave" data-layout="ETrackingWaveHomeLayout" data-model="app.MarkUserEntry" data-init="app.MarkUserEntry.init" data-show="app.MarkUserEntry.show">
    <header data-role="header">
        <div data-role="navbar">
           ETracking Wave - Mark Your Entries
            <a data-role="button" data-rel="modalview" href="#modalview-EntriesDetials" id="modalview-open-button">View Entry</a>
       </div>
  </header>
    <div class="controller-login">
        <div class="login-logo wagebase"></div>
        <form>
            <div class="login-holder">
                <div class="login-box">
                    <div class="card-section">
                        <div class="form-item icon">
                            <div class="form-text large">
                                     </div>
                        </div>
                        <label class="lblHeading" id="lblHeading">Make your Entry</label><br />
                        <label class="lblHeading" id="lblDateTime"></label>
                        <div class="form-joined">
                            <div class="form-item icon">
                                <div class="form-text large">
                                    <a id="btnDayBegin" data-role="button" class="button-kit xlarge green" data-bind="click: onMarkDayBeginEntry">Day Begin</a>
                                </div>
                            </div>
                            <div class="form-item icon">
                                <div class="form-text large">

                                    <a id="btnBreakBegin" data-role="button" href="#replyBreakEntryOptions" data-rel="actionsheet" class="button-kit xlarge green">Break</a>
                                    <a id="btnBreakEnd" data-role="button" data-bind="click: onMarkBreakEndEntry" class="hideEntryButtons button-kit xlarge green">Break End</a>

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
                                    <a id="btnDayEnd" data-role="button" class="button-kit xlarge green" data-bind="click: onMarkEndDayEntry">End of Day</a>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </form>
    </div>


    <div data-role="modalview" id="modalview-EntriesDetials" style="width: 90%; height: 60%" data-open="app.MarkUserEntry.showUserEntryDetails">
        <div data-role="header">
            <div data-role="navbar">
                <span>Today's Entries</span>
                <a data-click="closeModalViewLogin" data-role="button" data-align="right">Cancel</a>
            </div>
        </div>
        <div class="divCurrentDayEntries">
            <ul data-role="listview" id="lmyEntries" data-style="inset"></ul>
        </div>
    </div>
    <script id="myEntriesTemplate" type="text/x-kendo-template">
        <div class="divEntryRow">
            # if (EntryType == 'DB'){ #
                    <div class='rowUserEntryDetail'>
                        <p class="pRowEntry">
                           Your Day Started at  #:  kendo.toString(kendo.parseDate(EntryDateTime, 'yyyy-MM-dd HH:MM:SS'), 'HH:MM:ss') #
                        </p>
                    </div>
            # } else if (EntryType == 'DE'){ #
                   <div class='rowUserEntryDetail'>
                        <p class="pRowEntry">
                          You Day Ended at  #:  kendo.toString(kendo.parseDate(EntryDateTime, 'yyyy-MM-dd HH:MM:SS'), 'HH:MM:ss') #
                        </p>
                    </div>
            # } else if (EntryType == 'BE'){ #
                     <div class='rowUserEntryDetail'>
                        <p class="pRowEntry">
                          You have taken Break at  #:  kendo.toString(kendo.parseDate(EntryDateTime, 'yyyy-MM-dd HH:MM:SS'), 'HH:MM:ss') #
                        </p>
                    </div>
             # } else if (EntryType == 'BEnd'){ #
                     <div class='rowUserEntryDetail'>
                        <p class="pRowEntry">
                          You have taken Break at  #:  kendo.toString(kendo.parseDate(EntryDateTime, 'yyyy-MM-dd HH:MM:SS'), 'HH:MM:ss') #
                        </p>
                    </div>
             # } #
            
          <!-- #: EntryType #
           #:  kendo.toString(kendo.parseDate(EntryDateTime, 'yyyy-MM-dd HH:MM:SS'), 'HH:MM:ss') #
           #:  EntryType # 
           #:  kendo.toString(kendo.parseDate(EntryDateTime, 'yyyy-MM-dd HH:MM:SS'), 'MM/dd/yyyy') #
           #:  EntryDateTime #-->
        </div>
    </script>
    <script>
        function closeModalViewLogin() {
            $("#modalview-EntriesDetials").kendoMobileModalView("close");
        }
    </script>
</div>


.hideEntryButtons {
    display:none;
}
.showEntryButtons {
    display:block;
}
.pRowEntry {
    padding:0px;margin:0px;
}
