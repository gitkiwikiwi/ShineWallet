<template>
  <div class="app">
    <AppHeader fixed>
      <SidebarToggler class="d-lg-none" display="md" mobile />
      <b-link class="navbar-brand" to="#">
        <img class="navbar-brand-full" src="img/brand/logo.svg" width="89" height="25" alt="Logo">
        <img class="navbar-brand-minimized" src="img/brand/logo-small.svg" width="30" height="30" alt="Logo">
      </b-link>
      <SidebarToggler class="d-md-down-none" display="lg" />
      <b-navbar-nav class="d-md-down-none">
        <b-nav-item class="px-3" to="/dashboard">Dashboard</b-nav-item>
      </b-navbar-nav>
      <b-navbar-nav class="ml-auto">
        <DefaultHeaderDropdownAccnt/>
      </b-navbar-nav>
      <!-- <AsideToggler class="d-none d-lg-block" /> -->
      <!--<AsideToggler class="d-lg-none" mobile />-->
    </AppHeader>
    <div class="app-body">
      <AppSidebar fixed>
        <SidebarHeader/>
        <SidebarForm/>
        <SidebarNav :navItems="nav"></SidebarNav>
        <SidebarFooter/>
        <SidebarMinimizer/>
      </AppSidebar>
      <main class="main">
        <Breadcrumb :list="list"/>
        <div class="container-fluid">
          <router-view></router-view>
        </div>
      </main>
      <AppAside fixed>
        <!--aside-->
        <DefaultAside/>
      </AppAside>
    </div>
    <TheFooter>
      <!--footer-->
      <!--
      <div>
        <a href="https://coreui.io">CoreUI</a>
        <span class="ml-1">&copy; 2018 creativeLabs.</span>
      </div>
      -->
      <div class="ml-auto">
        <span class="mr-1">Made with ⚡️ by</span>
        <a href="https://twitter.com/cycryptr" target="_blank">Cycryptr</a>
      </div>
    </TheFooter>
  </div>
</template>

<script>
import nav from '@/_nav'
import { Header as AppHeader, SidebarToggler, Sidebar as AppSidebar, SidebarFooter, SidebarForm, SidebarHeader, SidebarMinimizer, SidebarNav, Aside as AppAside, AsideToggler, Footer as TheFooter, Breadcrumb } from '@coreui/vue'
import DefaultAside from './DefaultAside'
import DefaultHeaderDropdownAccnt from './DefaultHeaderDropdownAccnt'
import {WalletList} from '../models/collections/WalletList'

export default {
  name: 'DefaultContainer',
  components: {
    AsideToggler,
    AppHeader,
    AppSidebar,
    AppAside,
    TheFooter,
    Breadcrumb,
    DefaultAside,
    DefaultHeaderDropdownAccnt,
    SidebarForm,
    SidebarFooter,
    SidebarToggler,
    SidebarHeader,
    SidebarNav,
    SidebarMinimizer
  },
  data () {
    return {
      nav: nav.items,
      blockstack: window.blockstack,
      walletList: new WalletList()
    }
  },
  computed: {
    name () {
      return this.$route.name
    },
    list () {
      return this.$route.matched.filter((route) => route.name || route.meta.label)
    }
  },
  created () {
    this.loadWallets()
  },
  methods: {
    loadWallets () {
      console.log('retrieving wallet info')
      this.walletList.get(this.blockstack)
        .then(() => {
          this.walletList.models.forEach((wallet) => {
            this.addWalletToNav(wallet)
          })
        })
    },
    addWalletToNav (wallet) {
      let walletNav = this.nav.find(o => o.name === 'Wallets')
      let newWallet = {
        name: this.truncate(wallet.alias),
        url: '/wallets/view/' + wallet.id,
        icon: 'icon-wallet'
      }

      walletNav.children.findIndex(w => w.name === newWallet.name) === -1 ? walletNav.children.unshift(newWallet) : console.log('This wallet already exists')
    },
    truncate (input) {
      if (input.length > 16) { return input.substring(0, 14) + '...' } else { return input }
    }
  }
}
</script>
