<template>
  <div class="header-wrapper row m-0">
    <form
      class="form-inline search-full"
      action="#"
      method="get"
      :class="{ open: searchOpen }"
    >
      <div class="form-group w-100">
        <div class="Typeahead Typeahead--twitterUsers">
          <div class="u-posRelative">
            <input
              class="demo-input Typeahead-input form-control-plaintext w-100"
              type="text"
              v-on:keyup="searchterm"
              v-model="terms"
              placeholder="Search Cuba .."
              name="q"
              title=""
              autofocus
            />
            <div class="spinner-border Typeahead-spinner" role="status">
              <span class="sr-only">Loading...</span>
            </div>
            <feather
              class="close-search"
              type="x"
              @click="search_close()"
            ></feather>
          </div>
          <div
            :class="searchResult ? 'Typeahead-menu is-open' : 'Typeahead-menu'"
            v-if="menuItems.length"
          >
            <div
              class="ProfileCard u-cf"
              v-for="(menuItem, index) in menuItems.slice(0, 8)"
              :key="index"
            >
              <div class="ProfileCard-avatar header-search">
                <!-- <feather :type="menuItem.icon"></feather> -->
              </div>
              <div class="ProfileCard-details">
                <div class="ProfileCard-realName">
                  <span @click="removeFix()"
                    ><router-link
                      :to="{ path: menuItem.path }"
                      class="realname"
                      >{{ menuItem.title }}</router-link
                    ></span
                  >
                </div>
              </div>
            </div>
          </div>
          <div
            :class="
              searchResultEmpty ? 'Typeahead-menu is-open' : 'Typeahead-menu'
            "
          >
            <div class="tt-dataset tt-dataset-0">
              <div class="EmptyMessage">
                Your search turned up 0 results. Opps There are no result found.
              </div>
            </div>
          </div>
        </div>
      </div>
    </form>
    <div class="header-logo-wrapper">
      <div class="logo-wrapper">
        <router-link to="/">
          <img
            class="img-fluid"
            :src="require('@/assets/images/logo/logo.png').default"
            alt
          />
        </router-link>
      </div>

      <div class="toggle-sidebar" @click="toggle_sidebar">
        <feather
          class="status_toggle middle sidebar-toggle"
          type="sliders"
          id="sidebar-toggle"
        ></feather>
      </div>
    </div>

 

    <div class="left-header col horizontal-wrapper pl-0">

    </div>
    <div class="nav-right col-8 pull-right right-header p-0">
      <ul class="nav-menus">

        <li class="language-nav btn dropdown-toggle  btn-sm language-button"  style="background: #f8f8f8; border: initial;font-size: 0.8rem;color: #8f8f8f;padding: 0.6rem 1rem;border-radius: 50px;">
          <b-dropdown
            id="langddm"
            class="translate_wrapper ml-2"
            variant="light"
            size="sm"
            toggle-class="language-button"
          >
            <template slot="button-content">

              <span class="name">{{ $i18n.locale }}</span>
            </template>
            <b-dropdown-item
              v-for="(l, index) in localeOptions"
              :key="index"
              @click="changeLocale(l, l.direction)"
            >

              {{ l.name }}</b-dropdown-item
            >
          </b-dropdown>
        </li>


        <li
          class="onhover-dropdown"
          v-if="$store.getters.authUserRole[0].role == 'owner'"
        >
          <div class="notification-box">
            <feather type="bell"></feather
            ><span
              class="badge badge-pill badge-secondary"
              v-if="this.$store.getters.unreadNotificationNumber"
            >
              {{ this.$store.getters.unreadNotificationNumber }}</span
            >
          </div>
          <ul
            style="width: 330px"
            class="notification-dropdown onhover-show-div"
          >
            <li>
              <feather type="bell"></feather>
              <h6 class="f-18 mb-0">{{ $t("Notitications")}}</h6>
            </li>

            <li
              style="padding: 10px"
              v-for="(item, index) in this.$store.getters.unSeenNotification"
              :key="index"
            >
              <p>
                <i class="fa fa-circle-o mr-3 font-success"></i>
                <router-link
                  :to="{
                    name: 'notification',
                    params: { notificationId: item.created_at },
                  }"
                  style="margin: -10px"
                  >{{ item.title }}
                </router-link>
                <span
                  class="pull-right badge-light badge-pill"
                  style="color: #000"
                  >{{ handelTime(item.DateTime) }}</span
                >
              </p>
              <p style="margin-left: 20px">
                {{ item.body.substring(0, 30) + "..." }}
              </p>
            </li>

            <li>
              <router-link class="btn btn-link" :to="{ name: 'notification' }"
                >{{$t("Check all notification")}}</router-link
              >
            </li>
          </ul>
        </li>

        <li>
          <!-- <div class="mode">
            <i
              class="fa fa-moon-o"
              v-show="mixLayout == 'light-only'"
              @click="customizeMixLayout('dark-only')"
            ></i>
            <i
              class="fa fa-lightbulb-o"
              v-show="mixLayout == 'dark-only'"
              @click="customizeMixLayout('light-only')"
            ></i>
          </div> -->
        </li>

        <li class="maximize">
          <a
            class="text-dark"
            href="javascript:void(0)"
            @click="toggle_fullscreen()"
          >
            <feather type="maximize"></feather
          ></a>
        </li>
        <li class="profile-nav onhover-dropdown p-0 mr-0">
          <div class="media profile-media">
            <img
              class="b-r-10"
              style="width: 40px; height: 40px; border-radius: 50% !important"
              :src="
                this.$store.getters.USER['profile_picture']
                  ? '../../profile_pictures/' +
                    this.$store.getters.USER['profile_picture']
                  : '../../profile_pictures/DefaultProfile.jpg'
              "
              alt=""
            />
            <div class="media-body">
              <span> {{ this.$store.getters.USER.name }} </span>
              <p
                class="mb-0 font-roboto"
                v-for="(role, index) in this.$store.getters.authUserRole"
                :key="index"
              >
                {{ role.role }}
                <i class="middle fa fa-angle-down"></i>
              </p>
            </div>
          </div>
          <ul class="profile-dropdown onhover-show-div">
            <li>
              <router-link :to="{ name: 'profile' }">
                <feather type="user"></feather><span>{{$t("Account")}} </span>
              </router-link>
            </li>

            <li v-if="can('show-team-chat')">
              <router-link :to="{ name: 'TeamChat' }">
                <feather type="message-circle"></feather><span>{{$t("chat")}} </span>
              </router-link>
            </li>

            <li v-if="can('show-history')">
              <router-link :to="{ name: 'history' }">
                <feather type="coffee"></feather>
                <span>{{$t("history")}} </span>
              </router-link>
            </li>

            <li>
              <a
                ><feather type="log-in"></feather
                ><span @click.prevent="Logout">{{$t("Log out")}}</span></a
              >
            </li>
          </ul>
        </li>
      </ul>
    </div>

    <script class="result-template" type="text/x-handlebars-template">
      <div class="ProfileCard u-cf">
        <div class="ProfileCard-avatar">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="24"
            height="24"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
            class="feather feather-airplay m-0"
          >
            <path
              d="M5 17H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h16a2 2 0 0 1 2 2v10a2 2 0 0 1-2 2h-1"
            ></path>
            <polygon points="12 15 17 21 7 21 12 15"></polygon>
          </svg>
        </div>
        <div class="ProfileCard-details">
          <div class="ProfileCard-realName">
            name
          </div>
        </div>
      </div>
    </script>

    <script class="empty-template" type="text/x-handlebars-template">
      <div class="EmptyMessage">
        Your search turned up 0 results. This most likely means the backend is
        down, yikes!
      </div>
    </script>
  </div>
</template>
<script>
import axios from "axios";

//multi lang
//multi lang

var body = document.getElementsByTagName("body")[0];
import { mapState, mapActions } from "vuex";

import app from "../main";
import { defaultLocale, localeOptions } from "../constants/config";
import { getDirection, setDirection } from "../constants/config";

import moment from "moment";

export default {
  name: "Search",
  data() {
    return {
      localeOptions,

      terms: "",
      searchOpen: false,
      searchResult: false,
      searchResultEmpty: false,
      close_sidebar_var: false,
      clicked: false,
      mobile_toggle: false,
      mobile_search: false,
      openbonusUI: false,
      openLevelmenu: false,
      openlanguage: false,
      mobile_accordian: false,
      mixLayout: "light-only",
      userName: "",
      langIcon: localStorage.getItem("currentLanguageIcon") || "flag-icon-us",
    };
  },

  computed: {
    ...mapState({
      menuItems: (state) => state.menu.searchData,
      megamenuItems: (state) => state.menu.megamenu,
    }),

    user() {
      this.userName = this.$store.getters.USER.name;
    },

    role() {
      this.userName = this.$store.getters.USER.roles.name;
    },
  },

  methods: {

    ...mapActions(["setLang"]),

    changeLocale(locale, direction) {

      const currentDirection = getDirection().direction;

      if (direction !== currentDirection) {
        setDirection(direction);
      }

      this.setLang(locale);
    },

    handelTime(time) {
      const timeAgo = moment(time).fromNow();

      return timeAgo;
    },

    toggle_sidebar() {
      this.$store.dispatch("menu/opensidebar");
    },
    setNavActive(item) {
      this.$store.dispatch("menu/setBonusNavActive", item);
    },
    openlangpicker() {
      this.openlanguage = !this.openlanguage;
    },
    openlevelmenu() {
      this.openLevelmenu = !this.openLevelmenu;
    },
    openmegamenu() {
      this.openbonusUI = !this.openbonusUI;
    },
    closeBonusUI() {
      this.openbonusUI = false;
    },
    search_open() {
      this.searchOpen = true;
    },
    search_close() {
      this.searchOpen = false;
    },
    searchterm: function () {
      this.$store.dispatch("menu/searchTerm", this.terms);
    },

    mobileaccordian() {
      this.mobile_accordian = !this.mobile_accordian;
    },

    Logout() {
      this.$store.dispatch("userLogout");
    },

    addFix() {
      body.classList.add("offcanvas");
      this.searchResult = true;
    },
    removeFix() {
      body.classList.remove("offcanvas");
      this.searchResult = false;
      this.terms = "";
    },
    toggle_fullscreen() {
      if (
        (document.fullScreenElement && document.fullScreenElement !== null) ||
        (!document.mozFullScreen && !document.webkitIsFullScreen)
      ) {
        if (document.documentElement.requestFullScreen) {
          document.documentElement.requestFullScreen();
        } else if (document.documentElement.mozRequestFullScreen) {
          document.documentElement.mozRequestFullScreen();
        } else if (document.documentElement.webkitRequestFullScreen) {
          document.documentElement.webkitRequestFullScreen(
            Element.ALLOW_KEYBOARD_INPUT
          );
        }
      } else {
        if (document.cancelFullScreen) {
          document.cancelFullScreen();
        } else if (document.mozCancelFullScreen) {
          document.mozCancelFullScreen();
        } else if (document.webkitCancelFullScreen) {
          document.webkitCancelFullScreen();
        }
      }
    },
    customizeMixLayout(val) {
      this.mixLayout = val;
      this.$store.dispatch("layout/setLayout", val);
    },
  },

  watch: {
    "$i18n.locale"(to, from) {
      if (from !== to) {
        this.$router.go(this.$route.path);
      }
    },

    menuItems: function () {
      this.terms ? this.addFix() : this.removeFix();
      if (!this.menuItems.length) this.searchResultEmpty = true;
      else this.searchResultEmpty = false;
    },
  },
};
</script>

<style>
@media only screen and (max-width: 575.98px) {
    .profile-nav {
        display: block !important;
    }
}
</style>
