<template>
<!--Map Section Start-->
<section>

  <div class="container">
    <div class="row wrap">
      <div class="col-6 col-md-3">
          <b-navbar-brand class="navbar-brand"><img :src='"../assets/images/etho/logo.png"' height="60" alt="logo"></b-navbar-brand>

        <ul class="footer-widget__list mt-2" style="padding: 0;">
          <li>Freedom Of Data</li>
          <li><a href="mailto:admin@ether1.org" class="hover-style-link">admin@ether1.org</a></li>
          <li><a href="tel:888-700-3846" class="hover-style-link">+1.888.700.ETHO</a></li>
        </ul>
      </div>
      <div class="col-6 col-md-3">
        <h6 class="footer-widget__title mb-20">VPS Providers</h6>
        <ul class="footer-widget__list" style="padding: 0;">
          <li><a @click="openLink('https://control.cloud.co.za/aff.php?aff=61')" class="hover-style-link">VPS.co.za</a></li>
          <li><a @click="openLink('https://m.do.co/c/3d164b7256f7')" class="hover-style-link">Digital Ocean</a></li>
          <li><a @click="openLink('https://www.vultr.com/?ref=7613496')" class="hover-style-link">Vultr</a></li>
          <li><a @click="openLink('https://www.hetzner.com/')" class="hover-style-link">Hetzner</a></li>
          <li><a @click="openLink('https://contabo.com/')" class="hover-style-link">Contabo</a></li>
        </ul>
      </div>
      <div class="col-6 col-md-3">
        <h6 class="footer-widget__title mb-20">Quick links</h6>
        <ul class="footer-widget__list" style="padding: 0;">
          <li><a @click="openLink('https://ether1.org/whitepaper.pdf')" class="hover-style-link">Whitepaper</a></li>
          <li><a @click="openLink('https://github.com/etho-black/ethopay')" class="hover-style-link">ETHOPay</a></li>
          <li><a @click="openLink('https://explorer.ether1.org/')" class="hover-style-link">Explorer 1</a></li>
          <li><a @click="openLink('https://blocks.ether1.org/')" class="hover-style-link">Explorer 2</a></li>
          <li><a @click="openLink('https://docs.ether1.org')" class="hover-style-link">Documentation</a></li>
          <li><a @click="openLink('https://nodes.ether1.org')" class="hover-style-link">Node Dashboard</a></li>
          <li><a @click="openLink('https://bitcointalk.org/index.php?topic=3725742.0')" class="hover-style-link">Bitcointalk Thread</a></li>
        </ul>
      </div>
      <div class="col-6 col-md-3">
        <h6 class="footer-widget__title mb-20">Etho Protocol Teams</h6>
        <ul class="footer-widget__list" style="padding: 0;">
          <li><a @click="openLink('http://www.etho.black')" class="hover-style-link">Etho Black</a></li>
        </ul>
      </div>
    </div>
  </div>
</section>
<!--Map Section Ends-->
</template>
<script>
export default {
  name: 'Map',
  methods: {
    openLink(url) {
      window.open(url);
    },
  },
};
</script>

<style scoped>
  a {
    cursor: pointer;
  }
</style>
