<template>
  <div class="custamContaineer ">
    <v-snackbar v-for="(s, i) in OrderbookGetnoren" :key="i" timeout="2900" absolute bottom center  color="#00A1F2"
      v-model="snackbarHide" style="position: fixed; bottom: 0;">
      <p class="mb-0">Your BUY
        Order {{ s.norenordno }} for {{ s.tsym }} in {{ s.exch }} is {{ s.status }}.</p>
    </v-snackbar>
    <v-snackbar v-if="ErrorMessageSnackbar" class="mr-5 pa-0" height="20px" :timeout="-1" outlined
      transition="slide-x-reverse-transition" :value="true" absolute top dense right tile color="red accent-2">
      <div class="d-flex">
        <v-icon class="mr-2" color="warning" absolute outlined top right>mdi-alert-outline</v-icon>
        <p class="my-auto"> {{ errorMessageGet }}</p>
        <v-tooltip bottom color="black">
          <template v-slot:activator="{ on, attrs }">
            <v-btn v-bind="attrs" v-on="on" @click="ErrorMessageSnackbar = false"
              class="pa-0 ml-auto black--text float-right" width="fit-content" icon height="fit-content">
              <img src="@/assets/Closeicon.svg" />
            </v-btn>
          </template>
          <span class="white--text">Close</span>
        </v-tooltip>
      </div>
    </v-snackbar>
    <v-dialog v-model="BuyPopUpScreenNew" scrollable max-width="500px">
      <v-card>
        <v-form ref="form" v-model="valid">
          <v-card class="elevation-0 px-6 pb-5" :class="DataTableObjectGetItem.Label == 'EQUITY' ? 'pt-7' : 'pt-2'"
            height="" color="#f1f3f8" outlined>
            <v-row v-if="DataTableObjectGetItem.Label == 'OPTION'">
              <v-col align="end" class="pb-2">
                <v-btn @click="BuyPopUpScreenNew = false" class="pa-0 black--text mb-2" width="fit-content" icon
                  height="fit-content">
                  <v-icon  color="black" size="18">mdi-close</v-icon>
                </v-btn>
              </v-col>
            </v-row>

            <v-row>
              <v-col cols="7" md="8" class="pt-0 pr-0">
                <p class="mb-0 fs-16px  Ls-0-64px text-blackColor fw-600"
                  v-if="DataTableObjectGetItem.Label == 'OPTION' || DataTableObjectGetItem.Label == 'EQUITY'">
                  {{ DataTableObjectGetItem.stock.name }}</p>
                <p class="mb-0 fs-14px  Ls-0-64px text-blackColor fw-600"
                  v-if="DataTableObjectGetItem.Label == 'OPTION' || DataTableObjectGetItem.Label == 'EQUITY'">
                  {{ DataTableObjectGetItem.ltp_get.exch }} : ₹ {{ DataTableObjectGetItem.ltp_get.lp }}</p>
              </v-col>
              <v-col cols="5" md="4" class="pr-0 pr-sm-3 pt-0 ">
                <div class="float-right" v-if="DataTableObjectGetItem.Label == 'OPTION'">
                  <v-btn class="pa-0 elevation-0 ml-1 white--text mt-n2" dense small
                    style="height: 20px; min-width: 20px; border-radius: 4px" color="#43A833">
                    <span :class="BuySellSelectCheck == true ? 'font-weight-thin ' : 'font-weight-bold'">B</span></v-btn>

                  <label class="switch1 ml-2 ">
                    <input type="checkbox" v-model="BuySellSelectCheck" />
                    <span class="sliderr1 round"></span>
                  </label>

                  <v-btn class="pa-0 elevation-0 ml-2 white--text mt-n2" dense small
                    style="height: 20px; min-width: 20px; border-radius: 4px" color="#FF1717">
                    <span :class="BuySellSelectCheck == false ? 'font-weight-thin' : 'font-weight-bold'">S</span></v-btn>
                </div>
                <div class="float-right" v-else>
                  <v-btn @click="BuyPopUpScreenNew = false" class="pa-0 black--text mb-2" width="fit-content" icon
                    height="fit-content">
                    <v-icon  color="black" size="18">mdi-close</v-icon>
                  </v-btn>
                </div>
              </v-col>
            </v-row>
          </v-card>
          <v-card-text class="pa-0 pt-1 pb-3" style="height:fit-content;">
            <v-radio-group class="pa-0 pl-5 mt-3" v-model="RadioGroupmodel" color="black" dense row>
              <v-radio v-for="r in RadioGroupitems" :key="r" color="black" :value="r"><template v-slot:label>
                  <p class="mb-0 text-Greycolor fs-13px Lh-16 font-weight-regular"> {{ r }}</p>
                </template></v-radio>
            </v-radio-group>
            <p class="pl-6 mb-0  text-Greycolor fs-14px Lh-14px font-weight-regular ">Select order type</p>
            <v-chip-group class="ml-5 mt-1 chipOverallParent" v-model="VchipGroupIndexGet" mandatory
              active-class="BondOrdersChipActive">
              <v-chip v-for="b in OrderTypeChip" class="rounded-pill ml-1  elevation-0" :key="b"> <span
                  class="fs-13px font-weight-regular Lh-16 ">{{ b }}</span> </v-chip>
            </v-chip-group>
            <v-divider class="mt-3 mb-4 mx-6"></v-divider>
            <div class="px-5">
              <v-row>
                <v-col cols="6" class="TextfiledParentClass">
                  <p class="mb-3 fs-14px font-weight-regular Lh-16">Quantity</p>
                  <v-text-field v-model="ModelCusNameQuantity" :step="DataTableObjectGetItem.LotsizeCus"
                    :rules="quntutyRules" hide-spin-buttons hide-details="auto" class="fs-14px" type="number"
                    :min="DataTableObjectGetItem.LotsizeCus" background-color="#F1F3F8" solo rounded flat dense>
                    <template v-slot:prepend-inner>
                      <v-btn v-if="ModelCusNameQuantity > DataTableObjectGetItem.LotsizeCus" @click="checkDecrementData()"
                        class="mr-1" elevation="0" icon x-small fab dark height="18px" width="18px"
                        style="background-color: white;">
                        <v-icon Append size="13" color="#666666">mdi-minus</v-icon>
                      </v-btn>
                    </template>

                    <template v-slot:append>
                      <v-btn @click="checkIncrementData()" class="mr-1" elevation="0" icon x-small fab dark height="18px"
                        width="18px" style="background-color: white;">
                        <v-icon Append size="13" color="#666666">mdi-plus</v-icon>
                      </v-btn>
                    </template>
                  </v-text-field>
                </v-col>
                <v-col cols="6" class="ParrentClassTextFiled">
                  <p class="mb-3 fs-14px font-weight-regular Lh-16">Price</p>
                  <v-text-field v-if="VchipGroupIndexGet == 0" class="red--text CustamTextFiledCheckData" hide-spin-buttons readonly
                    hide-details="auto" value="Market" background-color="#F1F3F8" solo rounded flat dense>
                    <template v-slot:prepend-inner>
                      <v-btn class="mr-1" elevation="0" icon x-small fab dark height="18px" width="18px"
                        style="background-color: white;">
                        <v-icon Append size="13" color="#666666">mdi-currency-rupee</v-icon>
                      </v-btn>
                    </template>
                    <template v-slot:append>
                      <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 14 14" fill="none">
                        <path
                          d="M10.0625 4.8125H9.1875V3.0625C9.1875 1.85456 8.20794 0.875 7 0.875C5.79206 0.875 4.8125 1.85456 4.8125 3.0625V4.8125H3.9375V3.0625C3.9375 1.37112 5.30862 0 7 0C8.69137 0 10.0625 1.37112 10.0625 3.0625V4.8125Z"
                          fill="#666666" />
                        <path
                          d="M11.5938 5.6875H2.40625C1.80206 5.6875 1.3125 6.17706 1.3125 6.78125V12.9062C1.3125 13.5104 1.80206 14 2.40625 14H11.5938C12.1979 14 12.6875 13.5104 12.6875 12.9062V6.78125C12.6875 6.17706 12.1979 5.6875 11.5938 5.6875ZM7.4375 10.8754V11.8125C7.4375 12.054 7.2415 12.25 7 12.25C6.7585 12.25 6.5625 12.054 6.5625 11.8125V10.8754C5.62669 10.6339 5.06406 9.67925 5.30556 8.74344C5.54706 7.80763 6.50169 7.245 7.4375 7.4865C8.37331 7.728 8.93594 8.68263 8.69444 9.61844C8.53519 10.2349 8.05394 10.7161 7.4375 10.8754Z"
                          fill="#666666" />
                      </svg>
                    </template>
                  </v-text-field>
                  <v-text-field :rules="PriceRulesCheck" v-if="VchipGroupIndexGet == 1" v-model="PriceModelCustamize"
                    min="0" step="0.05" class="red--text" hide-spin-buttons hide-details="auto" type="number"
                    background-color="#F1F3F8" solo rounded flat dense>
                    <template v-slot:prepend-inner>
                      <v-btn class="mr-1" elevation="0" icon x-small fab dark height="18px" width="18px"
                        style="background-color: white;">
                        <v-icon Append size="13" color="#666666">mdi-currency-rupee</v-icon>
                      </v-btn>
                    </template>
                  </v-text-field>

                </v-col>
              </v-row>
              <v-row v-if="RadioGroupmodel == 'BO'">
                <v-col cols="6" class="">
                  <p class="mb-3 fs-14px font-weight-regular Lh-16">Stoploss price</p>
                  <v-text-field v-model="StopLossCustamize" :rules="StopLossCustamizeRules" step="1" hide-spin-buttons
                    hide-details="auto" class="" background-color="#F1F3F8" solo rounded flat dense type="number">
                    <template v-slot:prepend-inner>
                      <v-btn class="mr-1" elevation="0" icon x-small fab dark height="18px" width="18px"
                        style="background-color: white;">
                        <v-icon Append size="13" color="#666666">mdi-currency-rupee</v-icon>
                      </v-btn>
                    </template>
                  </v-text-field>
                </v-col>
                <v-col cols="6" class="">
                  <p class="mb-3 fs-14px font-weight-regular Lh-16">Target Price</p>
                  <v-text-field v-model="TargetCustamize" :rules="TargetCustamizeRules" step="1" hide-spin-buttons
                    hide-details="auto" class="" background-color="#F1F3F8" solo rounded flat dense type="number">
                    <template v-slot:prepend-inner>
                      <v-btn class="mr-1" elevation="0" icon x-small fab dark height="23px" width="23px"
                        style="background-color: white;">
                        <v-icon Append size="13" color="#666666">mdi-currency-rupee</v-icon>

                      </v-btn>
                    </template>
                  </v-text-field>
                </v-col>
              </v-row>

              <svg xmlns="http://www.w3.org/2000/svg" width="fit-content" height="2" viewBox="0 0 579 2" fill="none">
                <path
                  d="M578 1.5C578.276 1.5 578.5 1.27614 578.5 1C578.5 0.723858 578.276 0.5 578 0.5V1.5ZM0 1.5H1.50521V0.5H0L0 1.5ZM4.51562 1.5H7.52604V0.5H4.51562V1.5ZM10.5365 1.5H13.5469V0.5H10.5365V1.5ZM16.5573 1.5H19.5677V0.5H16.5573V1.5ZM22.5781 1.5H25.5885V0.5H22.5781V1.5ZM28.599 1.5H31.6094V0.5H28.599V1.5ZM34.6198 1.5H37.6302V0.5H34.6198V1.5ZM40.6406 1.5H43.651V0.5H40.6406V1.5ZM46.6615 1.5H49.6719V0.5H46.6615V1.5ZM52.6823 1.5H55.6927V0.5H52.6823V1.5ZM58.7031 1.5H61.7136V0.5H58.7031V1.5ZM64.724 1.5H67.7344V0.5H64.724V1.5ZM70.7448 1.5H73.7552V0.5H70.7448V1.5ZM76.7656 1.5H79.776V0.5H76.7656V1.5ZM82.7865 1.5H85.7969V0.5H82.7865V1.5ZM88.8073 1.5H91.8177V0.5H88.8073V1.5ZM94.8281 1.5H97.8385V0.5H94.8281V1.5ZM100.849 1.5H103.859V0.5H100.849V1.5ZM106.87 1.5H109.88V0.5H106.87V1.5ZM112.891 1.5H115.901V0.5H112.891V1.5ZM118.911 1.5H121.922V0.5H118.911V1.5ZM124.932 1.5H127.943V0.5H124.932V1.5ZM130.953 1.5H133.964V0.5H130.953V1.5ZM136.974 1.5H139.984V0.5H136.974V1.5ZM142.995 1.5H146.005V0.5H142.995V1.5ZM149.016 1.5H152.026V0.5H149.016V1.5ZM155.036 1.5H158.047V0.5H155.036V1.5ZM161.057 1.5H164.068V0.5H161.057V1.5ZM167.078 1.5H170.089V0.5H167.078V1.5ZM173.099 1.5H176.109V0.5L173.099 0.5V1.5ZM179.12 1.5H182.13V0.5H179.12V1.5ZM185.141 1.5H188.151V0.5H185.141V1.5ZM191.162 1.5H194.172V0.5H191.162V1.5ZM197.182 1.5H200.193V0.5H197.182V1.5ZM203.203 1.5H206.214V0.5H203.203V1.5ZM209.224 1.5H212.234V0.5H209.224V1.5ZM215.245 1.5H218.255V0.5H215.245V1.5ZM221.266 1.5H224.276V0.5H221.266V1.5ZM227.287 1.5H230.297V0.5H227.287V1.5ZM233.307 1.5H236.318V0.5H233.307V1.5ZM239.328 1.5H242.339V0.5H239.328V1.5ZM245.349 1.5H248.36V0.5H245.349V1.5ZM251.37 1.5H254.38V0.5H251.37V1.5ZM257.391 1.5H260.401V0.5H257.391V1.5ZM263.412 1.5H266.422V0.5H263.412V1.5ZM269.432 1.5H272.443V0.5H269.432V1.5ZM275.453 1.5H278.464V0.5H275.453V1.5ZM281.474 1.5H284.484V0.5H281.474V1.5ZM287.495 1.5H290.505V0.5L287.495 0.5V1.5ZM293.516 1.5H296.526V0.5H293.516V1.5ZM299.536 1.5H302.547V0.5H299.536V1.5ZM305.557 1.5H308.568V0.5H305.557V1.5ZM311.578 1.5H314.589V0.5H311.578V1.5ZM317.599 1.5H320.609V0.5H317.599V1.5ZM323.62 1.5H326.63V0.5H323.62V1.5ZM329.641 1.5H332.651V0.5H329.641V1.5ZM335.661 1.5H338.672V0.5H335.661V1.5ZM341.682 1.5H344.693V0.5H341.682V1.5ZM347.703 1.5H350.713V0.5H347.703V1.5ZM353.724 1.5H356.734V0.5H353.724V1.5ZM359.745 1.5H362.755V0.5H359.745V1.5ZM365.765 1.5H368.776V0.5H365.765V1.5ZM371.786 1.5H374.797V0.5H371.786V1.5ZM377.807 1.5H380.817V0.5H377.807V1.5ZM383.828 1.5H386.838V0.5H383.828V1.5ZM389.849 1.5H392.859V0.5H389.849V1.5ZM395.869 1.5H398.88V0.5H395.869V1.5ZM401.89 1.5H404.901V0.5H401.89V1.5ZM407.911 1.5H410.922V0.5H407.911V1.5ZM413.932 1.5H416.942V0.5H413.932V1.5ZM419.953 1.5H422.963V0.5H419.953V1.5ZM425.974 1.5H428.984V0.5H425.974V1.5ZM431.994 1.5H435.005V0.5H431.994V1.5ZM438.015 1.5H441.026V0.5H438.015V1.5ZM444.036 1.5H447.046V0.5H444.036V1.5ZM450.057 1.5H453.067V0.5H450.057V1.5ZM456.078 1.5H459.088V0.5H456.078V1.5ZM462.098 1.5H465.109V0.5H462.098V1.5ZM468.119 1.5H471.13V0.5H468.119V1.5ZM474.14 1.5H477.15V0.5H474.14V1.5ZM480.161 1.5H483.171V0.5H480.161V1.5ZM486.182 1.5H489.192V0.5H486.182V1.5ZM492.202 1.5H495.213V0.5H492.202V1.5ZM498.223 1.5H501.234V0.5H498.223V1.5ZM504.244 1.5H507.255V0.5H504.244V1.5ZM510.265 1.5H513.275V0.5H510.265V1.5ZM516.286 1.5H519.296V0.5H516.286V1.5ZM522.307 1.5H525.317V0.5H522.307V1.5ZM528.328 1.5H531.338V0.5H528.328V1.5ZM534.348 1.5H537.359V0.5H534.348V1.5ZM540.369 1.5H543.38V0.5H540.369V1.5ZM546.39 1.5H549.401V0.5H546.39V1.5ZM552.411 1.5H555.421V0.5H552.411V1.5ZM558.432 1.5H561.442V0.5H558.432V1.5ZM564.453 1.5H567.463V0.5H564.453V1.5ZM570.474 1.5H573.484V0.5H570.474V1.5ZM576.495 1.5L578 1.5V0.5H576.495V1.5Z"
                  fill="#D9DBDC" />
              </svg>
              <p v-if="DataTableObjectGetItem.Label == 'EQUITY'" class="mb-0 pt-1 text-Greycolor fs-14px Lh-14px font-weight-regular ">Note: Sorry! We don't allow sell transactions </p>
            </div>
          </v-card-text>
          <v-card-actions>
            <div class="ml-auto float-right text-end py-2 pr-4">
              <v-btn @click="validateFrom()" elevation="0" class="text-none" color="#43A833" rounded
                style=" border: 1px solid#F1F3F8">
                <span class="white--text fs-14px font-weight-regular Ls-0-112px">
                  Place Order</span>
              </v-btn>
            </div>
          </v-card-actions>
        </v-form>
      </v-card>
    </v-dialog>
    <v-container class="">
    <v-app-bar app fixed height="60px" elevation="0" style="border-bottom: 1px solid #bebebe; background-color: white" >
        <img alt="zebulogo" class=" text-center" src="@/assets/Zebu-logo.svg" width="80px" />
        <v-spacer></v-spacer>
        <v-tooltip bottom color="#000">
      <template v-slot:activator="{ on, attrs }">
        <v-btn medium class="btnRippleFalse mr-md-3" v-bind="attrs" v-on="on" icon @click="logout()"><v-icon size="20" color="black" class="textfnt">mdi-logout</v-icon></v-btn>
      </template>
      <span class="white--text fs-14px Lh-16 font-weight-regular">Logout</span>
    </v-tooltip>  
    </v-app-bar>
      <v-card class="brd-1 elevation-0 mt-3 d-none d-md-block" outlined height="" >
        <div class="pl-4 pr-3 py-4">
          <v-row>
            <v-col cols="7">
              <v-chip-group column v-model="MobBondChips" class="chipDeaultSizeCus  " mandatory active-class=" ChipActiveCuastom">
                <v-chip class=" text-center text-capitalize" @click="ChipFunCustam(tag)"
                  v-for="(tag ,i) in RecoStatusKeyPushArrary" :key="i" outlined>
                  {{ tag.toLowerCase() }}
                </v-chip>
              </v-chip-group>
            </v-col>
            <v-col cols="5" class="pr-0 pl-0 ">
              <div class="d-flex pt-2 ParrentClassTextFiled">
                <v-select @change="ChipFunCustam()" v-model="SelectDataCustamModel" class="elevation-0 fs-14px mr-3 green--text" fade  background-color="#F1F3F8" 
          :items="SelectEquityOption"  rounded hide-details flat dense solo append-icon="mdi-chevron-down" style="max-width:22%"
        ></v-select>
                <v-text-field class="pl-0 mr-3  d-none d-md-block" v-model="TableSearch" hide-details height="36px"
                  background-color="#F1F3F8" solo rounded flat dense style="max-width:176px">
                  <template v-slot:prepend-inner>
                    <img alt="search-icon" class="shrink" src="@/assets/searchicon.svg" width="20px" height="18px" />
                  </template>
                  <template v-slot:label>
                    <p class="text-blackColor fs-14px font-weight-medium Lh-16">Search Stocks</p>
                  </template>
                </v-text-field>
                <div style="max-width:168px">
                  <v-menu left v-model="MobiDatePickerMenuModel" :close-on-content-click="false" :nudge-right="40"
                    transition="scale-transition" offset-y min-width="auto">
                    <template v-slot:activator="{ on, attrs }">
                      <v-text-field class="text-blackColor fs-14px font-weight-medium Lh-16 mr-1" solo rounded flat dense
                        hide-details prepend-inner-icon="mdi-calendar" v-model="computedDateFormatted"
                        background-color="#F1F3F8" readonly v-bind="attrs" v-on="on"></v-text-field>
                    </template>
                    <v-date-picker v-model="date"
                      @change="MobiDatePickerMenuModel = false, ChipFunCustam()"></v-date-picker>
                  </v-menu>

                </div>
                <v-btn icon medium  class="mr-2" @click="FirstInitalRun()">
                  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="22" height="22"><path fill="#000" d="M16.63 7.05a6.78 6.78 0 0 0-11.2 3.29.49.49 0 0 1-.47.37H2.65a.48.48 0 0 1-.48-.57 10 10 0 0 1 16.74-5.37l1.44-1.44A.97.97 0 0 1 22 4v5.4c0 .54-.43.98-.97.98h-5.4a.97.97 0 0 1-.69-1.65l1.69-1.69ZM2.97 13.61h5.4a.97.97 0 0 1 .69 1.66l-1.69 1.68a6.78 6.78 0 0 0 11.2-3.29.49.49 0 0 1 .47-.37h2.31c.3 0 .53.27.48.57a10 10 0 0 1-16.74 5.37l-1.44 1.44A.97.97 0 0 1 2 20v-5.4c0-.54.43-.98.97-.98Z"></path></svg>
                </v-btn>
              </div>
            </v-col>
          </v-row>
        </div>
        <v-data-table fixed-header dense disable-pagination height="440" :search="TableSearch" :loading="loaderis1"
          :headers="HeadersItemsNew" :items="checkitem" hide-default-footer>
          <template v-slot:item="{ item }">
            <tr class="" @mouseover="selectItem(item)" @mouseleave="unSelectItem(item)">
              <td class="pt-5 pb-2 pr-0">
                <v-list class="pa-0" style="background-color:transparent;width:fit-content">
                  <v-list-item class="mb-auto pr-0 pl-0">
                    <v-list-item-avatar class="mb-auto ma-0" 
                      :color="item.user.profile_pic == 'https://prod-api-connect.stockants.comNone' || item.user.full_name == 'Vineet Chawla' ? '#999' : ''">
                      <p class="mb-0" v-if="item.user.profile_pic == 'https://prod-api-connect.stockants.comNone' || item.user.full_name == 'Vineet Chawla' ">
                        {{ item.user.full_name.slice(0, 1) }} </p>
                      <img v-else class="" :src="item.user.profile_pic.replace(/-connect/g, '')"  />
                    </v-list-item-avatar>
                    <!-- <v-list-item-avatar class="mb-auto ma-0" v-if="item.Label == 'OPTION'"
                      :color="item.strategy.user_details.profile_pic == 'https://prod-api-connect.stockants.comNone' ? '#999' : ''">
                      <p class="mb-0"
                        v-if="item.strategy.user_details.profile_pic == 'https://prod-api-connect.stockants.comNone'">
                        {{ item.strategy.user_details.full_name.slice(0, 1) }} </p>
                      <img v-else class="" :src="item.strategy.user_details.profile_pic.replace(/-connect/g, '')" />
                    </v-list-item-avatar> -->
                    <v-list-item-content class="pt-0 pb-0 pl-2 mb-auto">
                      <v-list-item-title>
                        <div class="d-flex">
                          <p class="mb-0 fs-14px mb-auto fw-600 text-blackColor Lh-16">
                            {{ item.user.full_name}} </p>
                          <img src="@/assets/TwitterTick.svg" class=" ml-1" width="15px" height="15px" />
                        </div>
                      </v-list-item-title>
                      <v-list-item-subtitle>
                        <p class="font-weight-medium fs-12px pt-0 mb-0 Lh-16 text-blackColor">{{ item.Label == 'EQUITY' ?
                          item.user.profession : item.strategy.user_details.profession }}
                          <v-tooltip color="black" bottom v-if="item.Label == 'EQUITY' ? item.user.total_reco : ''">
                            <template v-slot:activator="{ on, attrs }">
                              <span v-bind="attrs" v-on="on" class="font-weight-medium text-blackColor  fs-13px Lh-16">({{
                                item.Label == 'EQUITY' ? item.user.total_reco : '' }})</span>
                            </template>
                            <p class="mb-0 font-weight-medium white--text  fs-14px Lh-16 ">Total Recommendation
                              -{{ item.Label == 'EQUITY' ? item.user.total_reco : '' }} </p>
                          </v-tooltip>
                        </p>
                      </v-list-item-subtitle>
                    </v-list-item-content>
                  </v-list-item>
                </v-list>
              </td>
              <td class=" pb-2 pr-0">
                <p class="mb-0 fs-13px  text-blackColor font-weight-medium  Lh-16">{{ item.createNewDateCustam }}</p>
              </td>
              <td class="mb-auto pb-2 pr-0">
                <p class="fs-13px text-BlackColor font-weight-medium Lh-16">{{ item.stock.name }} <span v-if="item.Label == 'OPTION'" :class="item.Label == 'OPTION' && item.reco == 'BUY' ? 'green--text' :  'red--text'" class="fs-13px"> {{ item.reco}}</span></p>
                <div class="display-flex row ">
                  <v-card :color="item === selectedItem ? 'white' : '#F1F3F8'" height="18px" width=""
                    class="px-2 py-1 ml-3 mb-1" elevation="0">
                    <p class=" mb-0 text-center text-666Color fs-9  font-weight-regular Ls-0-9px Lh-12px  text-uppercase">
                      {{ item.Label }} </p>
                  </v-card>
                  <v-card :class="item.stock.sector.length > 25 ? 'mb-2' : ''"
                    :color="item === selectedItem ? 'white' : '#F1F3F8'" height="18px" width=""
                    v-if="item.Label == 'EQUITY'" class="px-2 py-1 ml-3 mb-1" elevation="0">
                    <p :style="{ 'font-size': item.stock.sector.length > 25 ? '8px' : '9px' }"
                      class=" mb-0 text-center text-666Color  font-weight-regular Ls-0-9px Lh-12px  text-uppercase"> {{
                        item.stock.sector }} </p>
                  </v-card>
                </div>
              </td>
              <td class="mb-auto pb-2">
                <p class="text-end fs-13px  text-blackColor font-weight-medium  Lh-16">₹{{ item.Label == 'EQUITY' ?
                  item.reco_open_price : item.entry_premium }} </p>
              </td>
              <td class="mb-auto pb-2">
                <p class="text-end fs-13px  text-blackColor font-weight-medium  Lh-16">₹{{ item.ltp_get ? item.ltp_get.lp
                  : '' }}</p>
              </td>

              <td class="mb-auto pb-2">
                <p class="text-end fs-13px  black--text font-weight-medium  Lh-16 mb-0">{{
                  item.PandLCalBefore.toFixed(2) }}</p>
                <p class="mb-0 pl-0 text-end fs-11px text-GreenText font-weight-regular  Lh-16" v-if="item.PandLCal > 0">
                  +{{ item.PandLCal.toFixed(2) }}%</p>
                <p class="mb-0 text-end fs-11px  text-Redcolor font-weight-medium  Lh-16" v-else>{{
                  item.PandLCal.toFixed(2) }}%</p>
                <!-- <p class="text-end fs-13px text-GreenText  font-weight-medium  Lh-16" v-if="(((item.ltp_get.lp-item.ltp_get.close)/item.ltp_get.close)*100) > 0">{{ (((item.ltp_get.lp-item.ltp_get.close)/item.ltp_get.close)*100).toFixed(2)}}%</p>
                <p class="text-end fs-13px text-Redcolor  font-weight-medium  Lh-16" v-else>{{ (((item.ltp_get.lp-item.ltp_get.close)/item.ltp_get.close)*100).toFixed(2)}}%</p> -->
              </td>

              <td class="mb-auto pb-2">
                <p class="mb-0  text-end fs-13px  text-blackColor font-weight-medium  Lh-16">₹{{ item.Label == 'EQUITY' ?
                  item.target_price : item.target_premium }}</p>
                <p class="mb-0 pl-0 text-end fs-11px text-GreenText font-weight-regular  Lh-16">
                  +{{ item.max_profit_in_percentage }}%</p>
              </td>
              <td class="mb-auto pb-2 pl-0">
                <p class="mb-0 text-end fs-13px  text-blackColor font-weight-medium  Lh-16">₹{{ item.Label == 'EQUITY' ?
                  item.stop_loss : item.stop_loss }}</p>
                <p class="mb-0 text-end fs-11px  text-Redcolor font-weight-medium  Lh-16">-
                  {{ item.max_loss_in_percentage }}%</p>
              </td>


              <td class="mb-auto pr-0 pb-2">
                <p class="PadAdjustLeft fs-13px font-weight-medium Lh-16 text-capitalize"
                  :class="item.reco_status == 'STOP LOSS TRIGGERED' || item.reco_status == 'TARGET ACHIEVED' ? ' mb-0' : ''"
                  :style="{ 'color': item.reco_status == 'LIVE' ? '#43A833' : 'black', }">{{ item.reco_status.toLowerCase() }}</p>
              </td>
              <td class="mb-auto pb-2 pr-2">
                <v-btn :disabled="!(item.reco_status == 'LIVE' || item.reco_status == 'YET TO LIVE')" @click="BuyPopUpScreenNew = true, OrderPlaceFunCtion(item)" x-small class=" elevation-0"
                  width="50px" style="background-color:#43A833">
                  <p class="mb-0 fs-13 font-weight-medium  Lh-16 white--text text-center">{{ item.Label == 'EQUITY' ? "BUY" : "TRADE" }}</p>
                </v-btn>
              </td>
            </tr>
          </template>
        </v-data-table>

      </v-card>
    </v-container>

    <div class="d-block d-md-none" v-if="DtataTableTrueCheck">
      <div class="px-4">
        <v-row class="pb-1">
          <v-col cols="12" class="pt-0 pr-0 " align="end">
        <v-btn icon medium  class="mr-2 " @click="FirstInitalRun()">
                  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="22" height="22"><path fill="#000" d="M16.63 7.05a6.78 6.78 0 0 0-11.2 3.29.49.49 0 0 1-.47.37H2.65a.48.48 0 0 1-.48-.57 10 10 0 0 1 16.74-5.37l1.44-1.44A.97.97 0 0 1 22 4v5.4c0 .54-.43.98-.97.98h-5.4a.97.97 0 0 1-.69-1.65l1.69-1.69ZM2.97 13.61h5.4a.97.97 0 0 1 .69 1.66l-1.69 1.68a6.78 6.78 0 0 0 11.2-3.29.49.49 0 0 1 .47-.37h2.31c.3 0 .53.27.48.57a10 10 0 0 1-16.74 5.37l-1.44 1.44A.97.97 0 0 1 2 20v-5.4c0-.54.43-.98.97-.98Z"></path></svg>
                </v-btn>
              </v-col>
              </v-row>
              <v-select @change="ChipFunCustam()" v-model="SelectDataCustamModel" class="elevation-0 mb-4 text-blackColor fs-14px font-weight-medium Lh-16" fade  background-color="#F1F3F8" 
          :items="SelectEquityOption"  rounded hide-details
          flat
          dense
          solo append-icon="mdi-chevron-down" 
        ></v-select>
        <v-text-field class="mb-4" hide-details height="36px" v-model="MobViewTextfileSearchStocks"
          background-color="#F1F3F8" solo rounded flat dense>
          <template v-slot:prepend-inner>
            <img alt="search-icon" class="shrink" src="@/assets/searchicon.svg" width="20px" height="18px" />
          </template>
          <template v-slot:label>
            <p class="text-blackColor fs-14px font-weight-medium Lh-16 pl-1">Search Stocks</p>
          </template>
        </v-text-field>
        <v-menu left v-model="DatePickerMenuModel" :close-on-content-click="false" :nudge-right="40"
          transition="scale-transition" offset-y min-width="auto">
          <template v-slot:activator="{ on, attrs }">
            <v-text-field class="text-blackColor fs-14px font-weight-medium Lh-16" solo rounded flat dense hide-details
              prepend-inner-icon="mdi-calendar" v-model="computedDateFormatted" background-color="#F1F3F8" readonly
              v-bind="attrs" v-on="on"></v-text-field>
          </template>
          <v-date-picker v-model="date" @change="DatePickerMenuModel = false, ChipFunCustam()"></v-date-picker>
        </v-menu>
        <v-chip-group v-model="MobBondChips" class="chipDeaultSizeCus mt-3 mb-2 " mandatory
          active-class=" ChipActiveCuastom">
          <v-chip class=" text-center text-capitalize" @click="ChipFunCustam(tag)" v-for="(tag, i) in RecoStatusKeyPushArrary"
            :key="i" outlined>
            {{ tag.toLowerCase() }}
          </v-chip>
        </v-chip-group>
      </div>
     
      <v-card class="elevation-0 mt-2">
        <div v-if="DataItemStocks">
          <div class="bacg-color pb-0">
            <v-card class="elevation-0 mb-0  px-4"
              v-for="(r, i ) in MobSeeMoreStocks ? MobSearchFilterItem.slice(0, 5) : MobSearchFilterItem  " :key="i">
              <v-list class="pa-0 pt-6" style="background-color:transparent">
                <v-list-item class="mb-auto pr-0 pl-0">
                  <v-list-item-avatar class="mb-auto ma-0"
                    :color="r.user.profile_pic == 'https://prod-api-connect.stockants.comNone' || r.user.full_name == 'Vineet Chawla' ? '#999' : ''">
                    <p class="mb-0" v-if="r.user.profile_pic == 'https://prod-api-connect.stockants.comNone' || r.user.full_name == 'Vineet Chawla'">
                      {{ r.user.full_name.slice(0, 1) }} </p>
                    <img v-else class="" :src="r.user.profile_pic.replace(/-connect/g, '')" />
                  </v-list-item-avatar>
                  <v-list-item-content class="pt-0 pb-0 pl-2 mb-auto">
                    <v-list-item-title>
                      <div class="d-flex">
                        <p class="mb-0 fs-14px mb-auto fw-600 text-blackColor Lh-16 text-uppercase">{{ r.user.full_name }}
                        </p>
                        <img src="@/assets/TwitterTick.svg" class=" ml-1" width="15px" height="15px" />
                        <p class="font-weight-medium fs-12px pt-0 mb-0 pl-2 Lh-16 text-blackColor" v-if="r.Label == 'EQUITY'">{{ r.user.profession }}
                         
                        </p> <span class="pl-1 text-Greycolor fs-12px fw-600 ">{{ r.recoDtaeNew }}</span>
                      </div>
                    </v-list-item-title>
                    <v-list-item-subtitle>
                      <p class="font-weight-medium fs-12px pt-0 mb-0 Lh-16 text-blackColor" v-if="r.Label == 'OPTION'">{{ r.user.profession }} </p>

                      <p v-if="r.user.total_reco" class="mb-0 font-weight-medium text-Greycolor  fs-14px Lh-16">Total
                        Recommendation
                        -{{ r.user.total_reco }} </p>
                        <p ></p>

                    </v-list-item-subtitle>
                  </v-list-item-content>
                </v-list-item>
              </v-list>
              <v-divider class="mt-1"></v-divider>
              <div class="pt-3 ">
                <div class="d-flex ">
                  <div>
                    <p class="mb-0 fs-15px fw-600 Lh-16 text-blackColor">{{ r.stock.name }} <span v-if="r.Label == 'OPTION'" :class="r.Label == 'OPTION' && r.reco == 'BUY' ? 'green--text' :  'red--text'" class="fs-14px font-weight-medium Lh-16"> {{ r.reco}}</span></p>
                    <div class=" py-2">
                      <v-card :color="r === selectedItem ? 'white' : '#F1F3F8'" height="18px" width="fit-content"
                        class="px-2 py-1  mb-1" elevation="0">
                        <p
                          class=" mb-0 text-center text-666Color fs-9  font-weight-regular Ls-0-9px Lh-12px  text-uppercase">
                          {{ r.Label }} </p>
                      </v-card>
                      <v-card :color="r === selectedItem ? 'white' : '#F1F3F8'" height="18px" v-if="r.stock.sector"
                        class="px-2 py-1" elevation="0" width="fit-content">
                        <p :style="{ 'font-size': r.stock.sector.length > 30 ? '8px' : '9px' }"
                          class=" mb-0 text-center text-666Color  font-weight-regular Ls-0-9px Lh-12px  text-uppercase">
                          {{ r.stock.sector }} </p>
                      </v-card>
                    </div>
                    <p class="mb-2 pt- fs-13px text-Greycolor font-weight-medium Lh-16">{{ r.createNewDateCustam }}</p>
                  </div>
                  <div class="ml-auto text-end">
                    <p class="mb-2 text-Greycolor fs-14px font-weight-medium Lh-12px">Status</p>
                    <p class=" mb-0 fw-600  fs-13px Lh-16 text-capitalize"
                      :style="{ 'color': r.reco_status == 'LIVE' ? '#43A833' : 'black' }">{{ r.reco_status.toLowerCase()
                      }}</p>
                  </div>
                </div>
                <v-divider></v-divider>
                <v-card class="d-flex elevation-0 mt-4 mb-1" >
                  <v-card class="elevation-0" width="43%">
                    <p class="mb-2 text-Greycolor fs-14px font-weight-medium Lh-12px">Price</p>
                    <p class="mb-2 text-blackColor fs-14px fw-600 Lh-16">₹ {{ r.reco_open_price }}</p>
                  </v-card>
                  <v-card class="elevation-0 " width="33%">
                    <p class="mb-2 text-Greycolor fs-14px font-weight-medium Lh-12px">LTP</p>
                    <p class="mb-1 text-blackColor fs-14px fw-600 Lh-16">₹ {{ r.ltp_get.lp }}</p>
                  </v-card>
                  <v-card class="elevation-0 text-end"  width="23%">
                    <p class="mb-2 text-Greycolor fs-14px font-weight-medium Lh-12px">P&L</p>

                    <p class="mb-1 text-blackColor fs-14px fw-600 Lh-16">{{ r.PandLCalBefore.toFixed(2) }}</p>
                    <p class="mb-1 text-blackColor fs-12px fw-600 Lh-16"
                      :class="r.PandLCal > 0 ? 'green--text' : 'red--text'"> {{ r.PandLCal.toFixed(2) }}%</p>
                  </v-card>

                </v-card>
                <v-divider class="mt-1 mb-3"></v-divider>
                <v-card class="d-flex elevation-0 mt-4 mb-4" >
                  <v-card class="elevation-0 " width="43%">
                    <p class="mb-2 text-Greycolor fs-14px font-weight-medium Lh-12px">Target Price</p>
                    <p class="mb-1 text-blackColor fs-14px fw-600 Lh-16">₹ {{ r.target_price }}</p>
                    <p class="mb-2 pl-0 fs-12px text-GreenText font-weight-medium  Lh-12px">
                      +{{ r.max_profit_in_percentage }}%</p>
                  </v-card>
                  <v-card class="elevation-0" width="33%">
                    <p class="mb-2 text-Greycolor fs-14px font-weight-medium Lh-12px">Sl Price</p>
                    <p class="mb-1 text-blackColor fs-14px fw-600 Lh-16">₹ {{ r.target_price }}</p>
                    <p class="mb-2 pl-0 fs-12px text-Redcolor font-weight-medium Lh-12px">
                      {{ r.max_loss_in_percentage }}%</p>
                  </v-card>

                  <v-card class="elevation-0 text-end" width="23%">
                    <v-btn :disabled="!(r.reco_status == 'LIVE' || r.reco_status == 'YET TO LIVE')" small class="ml-auto elevation-0" @click="BuyPopUpScreenNew = true, OrderPlaceFunCtion(r)"
                      style="background-color :#43A833">
                      <p class="mb-0 fs-13 font-weight-regular white--text Lh-16 ">{{ r.Label == 'EQUITY' ? "BUY" :
                        "TRADE" }}</p>
                    </v-btn>
                  </v-card>
                </v-card>
              </div>
            </v-card>

          </div>
          <div class="text-center btnRippleFalse " :class="MobSearchFilterItem == '' ? 'd-none' : ''"
            v-if="MobSearchFilterItem.length > 5">
            <v-btn class="text-none my-2"  text @click="MobSeeMoreStocks = !MobSeeMoreStocks">
              <p class="mb-0 fs-14px text-BLUEcolor fw-600" v-if="MobSeeMoreStocks">See more Stocks <v-icon
                  color="#0037B7"> mdi-chevron-down</v-icon> </p>
              <p class="mb-0 fs-14px text-BLUEcolor fw-600" v-else>See Less Stocks <v-icon color="#0037B7">
                  mdi-chevron-up</v-icon> </p>
            </v-btn>
            <!-- <v-divider class="mb-2"></v-divider> -->
          </div>

        </div>
        <v-card v-else class="d-flex elevation-0 text-center" flat height="60vh" tile>
          <v-card class="pa-2 align-self-center elevation-0 mx-auto" tile>
            <v-progress-circular :size="40" color="primary" indeterminate></v-progress-circular>
          </v-card>
        </v-card>
        <div v-if="MobSearchFilterItem.length == '' && DataItemStocks">
          <p class="text-capitalize fs-14px text-blackColor fw-600 Lh-16 text-center pt-15">Try search for different
            Stocks Name<br> Or<br>choose different Date</p>
        </div>
      </v-card>

    </div>
  </div>
</template>
<script>
import axios from "axios";
export default {
  data() {
    return {
      DtataTableTrueCheck:true,
      active: 0,
      BuySellSelectCheck: false,
      PriceModelCustamize: null,
      snackbarHide: false,
      OrderbookGetnoren: [],
      ssssssssssssssss: 0.10,
      StopLossCustamize: null,
      VchipGroupIndexGet: 0,
      valid: true,
      TargetCustamize: null,
      BuyPopUpScreenNew: false,
      AddStopLossCheckBox: true,
      OrderTypeChip: ["Market", "Limit"],
      responseSTorePlaceorder: [],
      DataTableObjectGetItem: [],
      RadioGroupmodel: "BO",
      AddValidQtyRadioGroupmodel: null,
      newGettokenExch: [],
      MobViewTextfileSearchStocks: "",
      DataItemStocks: false,
      MobiDatePickerMenuModel: false,
      MobBondChips: "All",
      MobSeeMoreStocks: true,
      TableSearch: '',
      loaderis1: true,
      selectedItem: false,
      RadioGroupitems: ["Intraday", "Delivery", "BO"],
      QunatityModel: null,
      SelectEquityOption: ['All', 'Equity', 'Option'],
      quntutyRules: [
        v => !!v || 'Quantity is required',
      ],
      PriceRulesCheck: [
        v => !!v || 'Price is required',
        // v => parseFloat(v) <= parseFloat(this.DataTableObjectGetItem.ltp_get.high) || `Price Cannot be Greater than Upper circiut Limit ${this.DataTableObjectGetItem.ltp_get.high} `,
        // v => parseFloat(v) >= parseFloat(this.DataTableObjectGetItem.ltp_get.low) || `Price Cannot be Greater than Lower circiut Limit ${this.DataTableObjectGetItem.ltp_get.low} `
      ],
      StopLossCustamizeRules: [
        v => !!v || 'Stoploss price is required',
        // v =>parseFloat(v) <= parseFloat(this.DataTableObjectGetItem.ltp_get.high ) || `Price Cannot be Greater than Upper circiut Limit ${this.DataTableObjectGetItem.ltp_get.high} `,
        // v =>parseFloat(v) >= parseFloat(this.DataTableObjectGetItem.ltp_get.low ) || `Price Cannot be Greater than Lower circiut Limit ${this.DataTableObjectGetItem.ltp_get.low} `
      ],
      TargetCustamizeRules: [
        v => !!v || 'Target  is required',
        // v =>parseFloat(v) <= parseFloat(this.DataTableObjectGetItem.ltp_get.high ) || `Price Cannot be Greater than Upper circiut Limit ${this.DataTableObjectGetItem.ltp_get.high} `,
        // v =>parseFloat(v) >= parseFloat(this.DataTableObjectGetItem.ltp_get.low ) || `Price Cannot be Greater than Lower circiut Limit ${this.DataTableObjectGetItem.ltp_get.low} `
      ],
      HeadersItemsNew: [
        { text: 'Expert', align: 'start', sortable: false, value: '', class: "TableHeaderFntColor", width: "15%" },
        { text: 'Date', align: 'start', sortable: false, value: '', class: "TableHeaderFntColor", width: "11%" },
        { text: 'Stock name', align: 'start', sortable: false, value: 'stock.name', class: "TableHeaderFntColor CustamwidthStockName", width: "30%" },
        { text: 'Price', sortable: false, value: '', align: 'end', class: "TableHeaderFntColor", width: "4%", },
        { text: 'LTP', sortable: false, value: '', align: 'end', class: "TableHeaderFntColor", width: "4%", },
        { text: 'P&L', sortable: false, value: '', align: 'end', class: "TableHeaderFntColor", width: "4%", },
        { text: 'Target', sortable: false, align: 'end', class: "TableHeaderFntColor", width: "4%" },
        { text: 'Sl', sortable: false, align: 'end', class: "TableHeaderFntColor", width: "8%" },
        { text: 'Status', sortable: false, class: "TableHeaderFntColor", width: "8%" },
        { text: 'Action', sortable: false, class: "TableHeaderFntColor", width: "6%" },
      ],
      getEquityData: [],
      tokenExchGetStore: [],
      rplacetext: '',
      date: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
      dateFormatted: this.formatDate((new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10)),
      DatePickerMenuModel: false,
      OptionData: [],
      NewarrayOptioArrary: [],
      DynamicNstore: '',
      checkitem: [],
      RecoStatusKeyPushArrary: [],
      NewNamecheck: "",
      ErrorMessageSnackbar: false,
      ModelCusNameQuantity: null,
      errorMessageGet: '',
      SelectDataCustamModel:"All",
      MobileCheckCheckItems:[]
    }
  },
  computed: {
    computedDateFormatted() {
      return this.formatDate(this.date)
    },
    MobSearchFilterItem() {
      return this.checkitem.filter(post => {
        return post.stock.name.toLowerCase().includes(this.MobViewTextfileSearchStocks.toLowerCase())
      })
      
    },
  },
  watch: {
    date() {
      this.dateFormatted = this.formatDate(this.date)
    },
  },
  created() {
 document.title = "zebu-stockexpertview"
    var url = new URL(window.location.href); ''
    var sacc = url.searchParams.get("sAccountId");
    var ucode = url.searchParams.get("ucode");
    var stok = url.searchParams.get("sToken");
    if (ucode) {
      this.load = true
    }
    else if (typeof sacc == 'string' && typeof stok == 'string') {
      let datao = JSON.stringify({
        "token": stok,
        "LoginId": sacc
      });
      let configo = {
        method: 'post',
        url: 'https://rekycbe.mynt.in/autho/kambala_auth',
        headers: {
          'Content-Type': 'application/json'
        },
        data: datao
      };

      let axiosthis = this;
      axios
        .request(configo)
        .then((response) => {
          if (response.data.clientid && response.data.token) {
            localStorage.setItem("usession", response.data.token);
            localStorage.setItem("userid", response.data.clientid);
            axiosthis.token = response.data.token;
            axiosthis.client_code = response.data.clientid;
            axiosthis.redirectpages();
          } else {
            localStorage.clear();
            axiosthis.goSso();
          }
          window.location.assign(window.location.href.split("?")[0]);
        })
        .catch((error) => {
          console.log(error);
          axiosthis.goSso();
        });
    } else {
      let decoded = decodeURIComponent(window.location.search);
      var actid = decoded.split("&")[0].split("=")[1]
      var token = decoded.split("=")[2]
      this.token = localStorage.getItem("usession");
      this.client_code = localStorage.getItem("userid");
      if (typeof actid == 'string' && typeof token == 'string') {
        localStorage.setItem("usession", token);
        localStorage.setItem("userid", actid);
        this.token = localStorage.getItem("usession");
        this.client_code = localStorage.getItem("userid");
        window.location.assign(window.location.href.split('?')[0])
      }
      let data = JSON.stringify({
        clientid: this.client_code,
        token: this.token,
      });
      let config = {
        method: "post",
        url: "https://rekycbe.mynt.in/autho/validate_session",
        headers: {
          "Content-Type": "application/json",
        },
        data: data,
      };

      let axiosthis = this;
      axios.request(config)
        .then((response) => {
          if (response.data.msg == "valid token") {
            // 
            axiosthis.redirectpages();

          } else {
            axiosthis.goSso();
          }
        })
        .catch((error) => {
          console.log(error);
          axiosthis.goSso();
        });
    }
  },
  methods: {
    logout() {
      let data = JSON.stringify({
        "clientid": this.client_code,
        "token": this.token
      });

      let config = {
        method: 'post',
        url: 'https://rekycbe.mynt.in/autho/logout',
        headers: {
          'Content-Type': 'application/json'
        },
        data: data
      };

      axios.request(config)
        .then((response) => {
          localStorage.clear();
          location.reload();
          console.log(response);
        })
        .catch((error) => {
          console.log(error);
        });
    },
    redirectpages() {
      this.FirstInitalRun();
    },
    goSso() {
      window.location.assign(`https://desk.mynt.in/?url=${window.location.href}`)
    },

    FirstInitalRun() {
      this.DataItemStocks=false
      this.BuyPopUpScreenNew =false
      this.loaderis1 =true
      this.checkitem = []
      
      var todayGetHourCustam = new Date().getHours()
if (todayGetHourCustam >= 9 && todayGetHourCustam <= 15) {
  this.optionData();
  setInterval(() => {
           this.optionData()
          }, 120000);
}
else {
  clearInterval(this.optionData())
}
    },
    checkIncrementData() {
      this.ModelCusNameQuantity = parseFloat(this.ModelCusNameQuantity) + this.DataTableObjectGetItem.LotsizeCus
    },
    checkDecrementData() {
      this.ModelCusNameQuantity = this.ModelCusNameQuantity > this.DataTableObjectGetItem.LotsizeCus ? this.ModelCusNameQuantity - this.DataTableObjectGetItem.LotsizeCus : this.DataTableObjectGetItem.LotsizeCus
    },
    OrderBookTrigger() {
      var axiosthis = this
      let data = `jData={"uid":"${this.client_code}"}&jKey=${this.token}`;
      let config = {
        method: 'post',

        url: 'https://go.mynt.in/NorenWClientTV/OrderBook',
        headers: {
          'Content-Type': 'text/plain'
        },
        data: data
      };

      axios.request(config)
        .then((response) => {
          var responseStore = response.data
          axiosthis.OrderbookGetnoren = responseStore.filter(c => this.responseSTorePlaceorder.find(s => s.norenordno == c.norenordno))
          axiosthis.snackbarHide = true
        })
        .catch((error) => {
          console.log(error);
        });
    },
    OrderPlaceFunCtion(v) {
this.BuySellSelectCheck = v.Label == 'OPTION' && v.direction == 'BUY' ? false : true
      this.RadioGroupmodel = "BO"
      this.VchipGroupIndexGet = "0"
      var NumberChangeLP = parseFloat(v.ltp_get.lp)
      v['LPNumber'] = NumberChangeLP.toFixed(2)
      v['LotsizeCus'] = v.Label == 'OPTION' ? parseFloat(v.stock.lot_size) : 1
      this.PriceModelCustamize = NumberChangeLP
      this.ModelCusNameQuantity = v['LotsizeCus']
      this.StopLossCustamize = Math.abs(v.reco_open_price - v.stop_loss).toFixed(2)
      this.TargetCustamize = Math.abs(v.reco_open_price - v.target_price).toFixed(2)
      this.DataTableObjectGetItem = v
    },
    validateFrom() {
      // var axiosthis = this
      if (this.$refs.form.validate() == !false) {
        // var DataItemCheckNew = this.DataTableObjectGetItem - this.DataTableObjectGetItem.target_price
        var ExchGet = this.DataTableObjectGetItem.ltp_get.exch
        var MktAndLMT = this.VchipGroupIndexGet == 0 ? "MARKET" : "LIMIT"
        var PriceMktAndLmt = this.VchipGroupIndexGet == 0 ? '0' : this.PriceModelCustamize
        var Quantitycus = this.ModelCusNameQuantity
        // var tokenGet = this.DataTableObjectGetItem.ltp_get.token
        var SymbolGetData = this.DataTableObjectGetItem.Label == 'EQUITY' ? `${this.DataTableObjectGetItem.stock.symbol}-EQ` : this.DataTableObjectGetItem.stock["Zebu-symbol"]
        var TranTypeBuyORSELL = this.DataTableObjectGetItem.Label == 'EQUITY' ? "BUY" : this.DataTableObjectGetItem.Label == 'OPTION' && this.BuySellSelectCheck == true ? "SELL" : "BUY"
        var OrderType = this.RadioGroupmodel == "Intraday" ? "INTRADAY" : this.RadioGroupmodel == "Delivery" && this.DataTableObjectGetItem.ltp_get.exch == 'NSE'  ? "DELIVERY" : this.RadioGroupmodel == "Delivery" && this.DataTableObjectGetItem.ltp_get.exch == 'NFO' ? "M" : this.RadioGroupmodel == "BO" ? 'BO' : ''
        var StopLossPriceGet = this.RadioGroupmodel == "BO" ? this.StopLossCustamize : ""
        var TargetPriceGet = this.RadioGroupmodel == "BO" ? this.TargetCustamize : ""
        var retCheckdata = "DAY"
    window.open(`https://mynt-ssologin.web.app/publisher?exch=${ExchGet}&prctyp=${MktAndLMT}&prd=${OrderType}&prc=${PriceMktAndLmt}&qty=${Quantitycus}&ret=${retCheckdata}&tsym=${SymbolGetData}&trantype=${TranTypeBuyORSELL}&bpprc=${TargetPriceGet}&blprc=${StopLossPriceGet}`, 'MYNT', 'width=500,height=680');
   
    this.BuyPopUpScreenNew =false
      }
    },
    equitydata() {
      var axiosthis = this;
      let data = JSON.stringify({
        "page_no": 1,
        "page_per_entries": 20


      });
      let config = {
        method: 'post',
        url: 'https://be.mynt.in/sants/getEquityData',
        headers: {
          'Content-Type': 'application/json'
        },
        data: data
      };
      axios.request(config)
        .then((response) => {
          var dataArrayOverAll = response.data.data
          for (let l = 0; l < dataArrayOverAll.length; l++) {
            var tokenExchget = ({ token: dataArrayOverAll[l].stock.token, exch: dataArrayOverAll[l].stock.exch })
            axiosthis.tokenExchGetStore.push(tokenExchget)
            dataArrayOverAll[l]['Label'] = "EQUITY"
            var custamCreateDate = dataArrayOverAll[l].created_at
            var CreatedDateCus = new Date(custamCreateDate)
            dataArrayOverAll[l]['getTime'] = CreatedDateCus.toLocaleTimeString()
            let equitycurrentDMY = new Date(dataArrayOverAll[l].created_at);
            let equitycDate = equitycurrentDMY.getDate() + '/' + (equitycurrentDMY.getMonth() + 1) + '/' + equitycurrentDMY.getFullYear();
            let equityhours = equitycurrentDMY.getHours();
            let equityam_pm = (equityhours >= 12) ? "PM" : "AM";
            if (equityhours > 12) {
              equityhours -= 12;
            }
            let equitycTime = equityhours + ":" + equitycurrentDMY.getMinutes() + ":" + equitycurrentDMY.getSeconds() + " " + equityam_pm;
            dataArrayOverAll[l]['createNewDateCustam'] = equitycDate + ' ' + equitycTime;
            dataArrayOverAll[l]['DateOnlyGet'] = CreatedDateCus.toLocaleDateString()
            dataArrayOverAll[l]['recoDtaeNew'] = axiosthis.differentDate(custamCreateDate)
            dataArrayOverAll[l]['token'] = dataArrayOverAll[l].stock.token
            dataArrayOverAll[l]['StockName'] = dataArrayOverAll[l].stock.name
            dataArrayOverAll[l]['StockExch'] = dataArrayOverAll[l].stock.exch
            if (!axiosthis.getEquityData.includes(dataArrayOverAll[l])) {
              axiosthis.getEquityData.push(dataArrayOverAll[l])
                }
            
          }
          axiosthis.ltpGetZebull();
          var unique = axiosthis.getEquityData.filter((obj, index) => {
            return index === axiosthis.getEquityData.findIndex(o => obj.reco_status === o.reco_status)
          });

          axiosthis.RecoStatusKeyPushArrary = []
          for (let k = 0; k < unique.length; k++) {
            axiosthis.RecoStatusKeyPushArrary.push(unique[k].reco_status)
          }
          axiosthis.RecoStatusKeyPushArrary.unshift("ALL")
        })
        .catch((error) => {
          console.log(error);
          if (error.message) {
            this.errorMessageGet = error.message
            axiosthis.loaderis1 = false
            axiosthis.ErrorMessageSnackbar = true
          }
        });
    },
    optionData() {
      var axiosthis = this;
      let data = JSON.stringify({
        "page_no": 1,
        "page_per_entries": 20
      });
      let config = {
        method: 'post',

        url: 'https://be.mynt.in/sants/getOptionData',
        headers: {
          'Content-Type': 'application/json'
        },
        data: data
      };
      axios.request(config)
        .then((response) => {
          axiosthis.tokenExchGetStore =[]
          axiosthis.equitydata();
          var responseOption = response.data.data
          axiosthis.NewarrayOptioArrary = responseOption
          axiosthis.getEquityData = []
          for (let z = 0; z < axiosthis.NewarrayOptioArrary.length; z++) {
            var tokenExchget = ({ token: axiosthis.NewarrayOptioArrary[z].data[0].stock.token, exch: "NFO" })
            axiosthis.tokenExchGetStore.push(tokenExchget)
            axiosthis.NewarrayOptioArrary[z].data[0]['Label'] = "OPTION"
            axiosthis.NewarrayOptioArrary[z].data[0]['token'] = axiosthis.NewarrayOptioArrary[z].data[0].stock.token
            var OptioncustamCreateDate = axiosthis.NewarrayOptioArrary[z].data[0].created_date
            var OptionCreatedDateCus = new Date(OptioncustamCreateDate)
            let current = new Date(axiosthis.NewarrayOptioArrary[z].data[0].created_date);
            let cDate = current.getDate() + '/' + (current.getMonth() + 1) + '/' + current.getFullYear();
            let hours = current.getHours();
            let am_pm = (hours >= 12) ? "PM" : "AM";
            if (hours > 12) {
              hours -= 12;
            }
            let cTime = hours + ":" + current.getMinutes() + ":" + current.getSeconds() + " " + am_pm;
            axiosthis.NewarrayOptioArrary[z].data[0]['createNewDateCustam'] = cDate + ' ' + cTime;
            axiosthis.NewarrayOptioArrary[z].data[0]['DateOnlyGet'] = OptionCreatedDateCus.toLocaleDateString()
            var expiryDateInital = axiosthis.NewarrayOptioArrary[z].data[0].stock.expiry
            var expiryDateCustamizeCheck = new Date(expiryDateInital)
            axiosthis.NewarrayOptioArrary[z].data[0]['exit_price'] = axiosthis.NewarrayOptioArrary[z].data[0].status_action_price
            axiosthis.NewarrayOptioArrary[z].data[0]['ExpiryDateCustam'] = expiryDateCustamizeCheck.toLocaleDateString()
            axiosthis.NewarrayOptioArrary[z].data[0]['getTime'] = OptionCreatedDateCus.toLocaleTimeString()
            axiosthis.NewarrayOptioArrary[z].data[0]['recoDtaeNew'] = axiosthis.differentDate(OptioncustamCreateDate)
            axiosthis.NewarrayOptioArrary[z].data[0]['reco_status'] = axiosthis.NewarrayOptioArrary[z].data[0].status
            axiosthis.NewarrayOptioArrary[z].data[0].stock['name'] = axiosthis.NewarrayOptioArrary[z].data[0].stock.symbol
            axiosthis.NewarrayOptioArrary[z].data[0]['reco_open_price'] = axiosthis.NewarrayOptioArrary[z].data[0].entry_premium
            axiosthis.NewarrayOptioArrary[z].data[0]['target_price'] = axiosthis.NewarrayOptioArrary[z].data[0].target_premium
            axiosthis.NewarrayOptioArrary[z].data[0]['user'] = axiosthis.NewarrayOptioArrary[z].data[0].strategy.user_details
            if (axiosthis.NewarrayOptioArrary[z].data[0].status == 'STOPLOSS_TRIGGERED') {
              axiosthis.NewarrayOptioArrary[z].data[0]['reco_status'] = 'STOP LOSS TRIGGERED'
            }
            else if (axiosthis.NewarrayOptioArrary[z].data[0].status == 'TARGET_ACHIEVED') {
              axiosthis.NewarrayOptioArrary[z].data[0]['reco_status'] = 'TARGET ACHIEVED'
            }
            else if (axiosthis.NewarrayOptioArrary[z].data[0].status == "TARGET_NOT_ACHIEVED") {
              axiosthis.NewarrayOptioArrary[z].data[0]['reco_status'] = 'TARGET NOT ACHIEVED'
            }
            else if (axiosthis.NewarrayOptioArrary[z].data[0].status == "YET_TO_LIVE") {
              axiosthis.NewarrayOptioArrary[z].data[0]['reco_status'] = 'YET TO LIVE'
            }
            axiosthis.NewarrayOptioArrary[z].data[0]['reco'] = axiosthis.NewarrayOptioArrary[z].data[0].direction
if (!axiosthis.getEquityData.includes(axiosthis.NewarrayOptioArrary[z].data[0])) {
  axiosthis.getEquityData.push(axiosthis.NewarrayOptioArrary[z].data[0])
                }
          }
        })
        .catch((error) => {
          if (error.message) {
            this.errorMessageGet = error.message
            axiosthis.loaderis1 = false
            axiosthis.ErrorMessageSnackbar = true
          }
        });
    },

    ChipFunCustam(n) {
      this.DynamicNstore = n ? n : this.DynamicNstore
  
      // if (this.loaderis1) {
        
      // }
      // this.DataItemStocks = true
    //   if ( this.DtataTableTrueCheck) {
    //     for (var i = 1; i < this.getEquityData.length; i++) {
    // for (var j = 0; j < i; j++) {
    //     if (this.getEquityData[j].getTime   < this.getEquityData[i].getTime) {
    //         var x = this.getEquityData[i];
    //         this.getEquityData[i] = this.getEquityData[j];
    //         this.getEquityData[j] = x;
    //     }
    //   }
    //   console.log("kkkkkkkkkkkkkkkk", this.getEquityData);
    // }

    //   }
      // }

      // if (this.DataItemStocks) {
        this.getEquityData.sort(function (a, b) {
        return new Date(b.DateOnlyGet + " " + b.getTime) - new Date(a.DateOnlyGet + " " + a.getTime);
      });
      // } 
      //   this.getEquityData.sort(function (a, b) {
      //   return new Date(b.DateOnlyGet + " " + b.getTime) - new Date(a.DateOnlyGet + " " + a.getTime);
      // });
      
      

    //   if (this.DataItemStocks) {
    //      for (var i = 1; i < this.getEquityData.length; i++)
    // for (var j = 0; j < i; j++)
    //     if (this.getEquityData[j].getTime   < this.getEquityData[i].getTime) {
    //         var x = this.getEquityData[i];
    //         this.getEquityData[i] = this.getEquityData[j];
    //         this.getEquityData[j] = x;
    //     }
    //   }
    //   for (var i = 1; i < this.getEquityData.length; i++)
    // for (var j = 0; j < i; j++)
    //     if (this.getEquityData[j].getTime   < this.getEquityData[i].getTime) {
    //         var x = this.getEquityData[i];
    //         this.getEquityData[i] = this.getEquityData[j];
    //         this.getEquityData[j] = x;
    //     }

      let text = new Date(this.date)
      this.rplacetext = text.toLocaleDateString();
      if (this.DynamicNstore === 'ALL' && this.SelectDataCustamModel === 'All' || this.DynamicNstore === 'ALL' && this.SelectDataCustamModel === 'Equity' ||  this.DynamicNstore === 'ALL' && this.SelectDataCustamModel === 'Option'  ) {
        this.checkitem = this.getEquityData.filter((x) => {
          return  this.SelectDataCustamModel === 'All' && this.DynamicNstore === 'ALL' ? x.DateOnlyGet == this.rplacetext && (x.ltp_get.lp !== "0.00" && x.ltp_get.lp !== "0")  : this.SelectDataCustamModel === 'Equity' 
          && this.DynamicNstore === 'ALL' ? x.DateOnlyGet == this.rplacetext && x.Label == 'EQUITY' :
          this.SelectDataCustamModel === 'Option' && this.DynamicNstore === 'ALL' ? x.DateOnlyGet == this.rplacetext && x.Label == 'OPTION' && (x.ltp_get.lp !== "0.00" && x.ltp_get.lp !== "0") : '' 
        });
    }
      else {
        this.checkitem = this.getEquityData.filter((x) => { 
          return this.SelectDataCustamModel === 'Equity' && this.DynamicNstore ? x.reco_status == this.DynamicNstore && x.DateOnlyGet == this.rplacetext && x.Label == 'EQUITY' : 
          this.SelectDataCustamModel === 'Option' && this.DynamicNstore ? x.reco_status == this.DynamicNstore && x.DateOnlyGet == this.rplacetext && x.Label == 'OPTION' && (x.ltp_get.lp !== "0.00" && x.ltp_get.lp !== "0")  : 
          this.SelectDataCustamModel === 'All' && this.DynamicNstore ? x.reco_status == this.DynamicNstore && x.DateOnlyGet == this.rplacetext && (x.ltp_get.lp !== "0.00" && x.ltp_get.lp !== "0") : ''
        });
      }
    },
    ltpGetZebull() {
      var axiosthis = this
      let data = JSON.stringify({
        "data": this.tokenExchGetStore
      });
      let config = {
        method: 'post',
        url: 'https://asvr.mynt.in/bcast/GetLtp',
        headers: {
          'Content-Type': 'application/json'
        },
        data: data
      };
      axios.request(config)
        .then((response) => {
          if (response.data && response.data.data ) {
            let data = response.data.data
            axiosthis.newGettokenExch = axiosthis.getEquityData
            axiosthis.getEquityData = []
            for (let index = 0; index < axiosthis.newGettokenExch.length; index++) {
              var vvvvvvvbbbbb = axiosthis.newGettokenExch[index].token
              if (axiosthis.newGettokenExch[index]) {
              if ( axiosthis.newGettokenExch[index].token == data[vvvvvvvbbbbb].token) {
                axiosthis.newGettokenExch[index]['ltp_get'] = data[vvvvvvvbbbbb]
                if (axiosthis.newGettokenExch[index].reco_status == 'STOP LOSS TRIGGERED') {
                  var slPlPercentage =   axiosthis.newGettokenExch[index].stop_loss - axiosthis.newGettokenExch[index].reco_open_price
                  axiosthis.newGettokenExch[index]['PandLCalBefore'] = slPlPercentage
                  axiosthis.newGettokenExch[index]['PandLCal'] = (slPlPercentage / axiosthis.newGettokenExch[index].reco_open_price) * 100
                }
                else if (axiosthis.newGettokenExch[index].reco_status == 'TARGET ACHIEVED') {
                  var targetAchivedPercentage =   axiosthis.newGettokenExch[index].target_price - axiosthis.newGettokenExch[index].reco_open_price
                  axiosthis.newGettokenExch[index]['PandLCalBefore'] = targetAchivedPercentage
                  axiosthis.newGettokenExch[index]['PandLCal'] = (targetAchivedPercentage / axiosthis.newGettokenExch[index].reco_open_price) * 100
                }
                else if (axiosthis.newGettokenExch[index].reco_status == 'EXIT') {
                  var ExitPricedPercentage =   axiosthis.newGettokenExch[index].exit_price - axiosthis.newGettokenExch[index].reco_open_price
                  axiosthis.newGettokenExch[index]['PandLCalBefore'] = ExitPricedPercentage
                  axiosthis.newGettokenExch[index]['PandLCal'] = (ExitPricedPercentage / axiosthis.newGettokenExch[index].reco_open_price) * 100
                }

                else if (axiosthis.newGettokenExch[index]) {
                  var LivePercentage = axiosthis.newGettokenExch[index].ltp_get.lp - axiosthis.newGettokenExch[index].reco_open_price
                  axiosthis.newGettokenExch[index]['PandLCalBefore'] = LivePercentage
                  axiosthis.newGettokenExch[index]['PandLCal'] = (LivePercentage / axiosthis.newGettokenExch[index].reco_open_price) * 100
                }

                var someDataPush = axiosthis.newGettokenExch[index]
                if (!axiosthis.getEquityData.includes(someDataPush)) {
                  axiosthis.getEquityData.push(someDataPush)
                }
              }
            }
            }
            axiosthis.loaderis1 = false
            axiosthis.DataItemStocks = true
            this.NewNamecheck = this.RecoStatusKeyPushArrary[0] == 'ALL' && !this.DynamicNstore  ? "ALL" :  this.DynamicNstore
            axiosthis.ChipFunCustam(this.NewNamecheck);
          }
        })
        .catch((error) => {
          if (error.message) {
            this.errorMessageGet = error.message
            axiosthis.loaderis1 = false
            axiosthis.ErrorMessageSnackbar = true
          }

        });

    },
    formatDate(date) {
      if (!date) return null
      const [year, month, day] = date.split('-')
      return `${day}/${month}/${year}`
    },
    parseDate(date) {
      if (!date) return null
      const [day, month, year] = date.split('/')
      return `${day}-${month.padStart(2, '0')}-${year.padStart(2, '0')}`
    },
    selectItem(item) {
      this.selectedItem = item
    },
    unSelectItem() {
      this.selectedItem = false
    },
    differentDate(date) {
      var date1 = new Date(date);
      var date2 = new Date();
      var ov = Math.abs(date2.getTime() - date1.getTime())
      var mt = Math.round(ov / 3600000)
      var dd = mt > 60 ? Math.round(mt / 24) : 0;
      var mm = dd > 30.4166667 ? Math.round(dd / 30.4166667) : 0;
      var yy = mm > 12 ? Math.round(dd / 365) : 0;
      return `${yy != 0 ? yy : mm != 0 ? mm : dd != 0 ? dd : mt != 0 ? mt : 0} ${yy != 0 ? yy > 1 ? 'Yr' : 'Yr' : mm != 0 ? mm > 1 ? 'Month' : 'month' : dd != 0 ? dd > 1 ? 'days' : 'day' : mt > 1 ? 'hr' : 'Min'} `
    },
  }
}
</script>
<style>
html,
body,
.v-application,
.v-application .display-1,
.v-application .headline,
.v-application .title,
.v-application .subtitle-1,
.v-application .subtitle-2,
.v-application .body-1,
.v-application .body-2,
.v-application .caption,
.v-application .overline {
  font-family: 'Inter var', sans-serif !important;
  /* -webkit-font-smoothing: antialiased !important;
  -moz-osx-font-smoothing: grayscale !important; */
}
</style>

<style lang="scss">
.HeightCustamaize {
  height: calc(87vh - 30px) !important;
}
@media (max-width: 1264px) {
  .CustamwidthStockName {
    width: fit-content !important;
  }

  .custamContaineer {
    .container {
      max-width: none !important;
    }

  }
}
// @media (max-width: 400px) {
.ParrentClassTextFiled {
  .v-text-field--rounded > .v-input__control > .v-input__slot {
    padding: 0 13px !important; 
  }
 } 
// }
.fs-8px {
  font-size: 8px !important
}

.fs-9 {
  font-size: 9px !important
}

.fs-10 {
  font-size: 10px !important
}

.fs-11px {
  font-size: 11px !important;
}

.fs-12px {
  font-size: 12px !important;
}

.fs-13px {
  font-size: 13px !important;
}

.fs-14px {
  font-size: 14px !important;
}

.fs-15px {
  font-size: 15px !important;
}

.fs-16px {
  font-size: 16px !important;
}

.fs-18px {
  font-size: 18px !important;
}

.fs-20 {
  font-size: 20px !important
}

.Ls-0_07 {
  letter-spacing: -0.07px !important;
}

.Ls-0-112px {
  letter-spacing: -0.112px !important;
}

.Ls-0-32Px {
  letter-spacing: -0.32px !important;
}

.Ls_004 {
  letter-spacing: -0.4px !important;
}

.Ls_006 {
  letter-spacing: -0.06px !important;
}

.Lh-12px {
  line-height: 12px !important;
}

.Lh-14px {
  line-height: 14px !important;
}

.Lh-16 {
  line-height: 16px !important;
}

.Ls-0-9px {
  letter-spacing: 0.9px !important;
}

.Ls-128px {
  letter-spacing: -0.128px;
}

.LabelclassCustomColorFnt {
  color: #000 !important;
  font-size: 14px !important;
  font-weight: 469 !important;
  line-height: 16px !important;
}

.mdiDownArrowMarginCus {
  .mdi-chevron-down::before {
    margin: 0px !important;
  }
}

.text-B1B1B1 {
  color: #B1B1B1 !important;
}

.text-blackColor {
  color: #000 !important;
}

.text-blackSecondColor {
  color: #1F2837 !important;
}
.text-GreenText {
  color: #43A833 !important;
}

.text-BLUEcolor {
  color: #0037B7 !important;
}

.text-282B2F {
  color: #282B2F !important;
}

.text-Greycolor {
  color: #666 !important;
}

.text-Redcolor {
  color: #FD2E2E !important;
}

.fw-600 {
  font-weight: 600 !important;
}

.bacg-color {
  background-color: #F1F3F8 !important;
}

.brd-1 {
  border: 1px solid #DDD !important;
}

.TableHeaderFntColor {
  color: #666 !important;
  font-size: 13px !important;
  font-weight: 469 !important;
  line-height: 16px !important;
  background-color: #FAFBFF !important;
  height: 45px !important;
}

.MessageInpuslotCus {
  .v-messages {
    min-height: 0px !important;
  }

  .v-input__slot {
    margin-bottom: 0px !important;
  }
}

.btnRippleFalse {
  .v-btn::before {
    background-color: transparent !important;
  }
}

.PadAdjustLeft {
  padding-left: 3px !important;
}

// v-chip
.chipDeaultSizeCus {
  .v-chip.v-chip--outlined.v-chip.v-chip {
    padding: 5px 20px 5px 20px !important;
    width: fit-content !important;
  }

  .v-chip.v-size--default {
    height: 27px !important;
  }

  .v-chip:before {
    background-color: transparent !important;
  }
}

.ChipActiveCuastom {
  border: 2px solid #000 !important;
  color: #000 !important;
  font-size: 14px !important;
  font-weight: 500 !important;
  line-height: 100% !important;
  letter-spacing: -0.28px !important;
}

.chipOverallParent {
  .v-chip.v-size--default {
    height: 24px !important;
  }

  .theme--light.v-chip:not(.v-chip--active) {
    background-color: #F1F3F8 !important;
  }
}

.BondOrdersChipActive {
  color: #FFF !important;
  background-color: #000000 !important;
  font-size: 13px !important;
}

.TextfiledParentClass {
  .v-text-field.v-text-field--solo .v-input__control input {
    text-align: center !important;
    color: #1F2837 !important;
    font-weight: 600 !important;
  }
}
</style>
<style lang="scss">
.switch1 {
  position: relative;
  display: inline-block;
  width: 56px;
  height: 24px;
}

.switch1 input {
  opacity: 0;
  width: 0;
  height: 0;
}

.switch1 {
  position: relative;
  display: inline-block;
  width: 45px;
  height: 20px;
}

.sliderr1 {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ffffff;
  -webkit-transition: 0.4s;
  transition: 0.4s;
}

.sliderr1:before {
  position: absolute;
  content: "";
  height: 11px;
  width: 18px;
  left: 3px;
  bottom: 5px;
  background-color: rgb(0, 0, 0);
  -webkit-transition: 0.4s;
  transition: 0.4s;
}

input:checked+.sliderr1:before {
  -webkit-transform: translateX(19px);
  -ms-transform: translateX(19px);
  transform: translateX(19px);
}

.sliderr1.round {
  border-radius: 30px;
}

.sliderr1.round:before {
  border-radius: 40%;
}

.input-group .input-group__input {
  font-size: 1px !important;
  width: 10px;
}
</style>






























































