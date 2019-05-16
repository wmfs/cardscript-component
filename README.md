# Cardscript Component


[![Tymly Cardscript](https://img.shields.io/badge/tymly-cardscript-blue.svg)](https://tymly.io/)
[![Build Status](https://travis-ci.com/wmfs/cardscript-component.svg?branch=master)](https://travis-ci.com/wmfs/cardscript-component)
[![npm (scoped)](https://img.shields.io/npm/v/@wmfs/cardscript-component.svg)](https://www.npmjs.com/package/@wmfs/cardscript-component) 
[![Dependabot badge](https://img.shields.io/badge/Dependabot-active-brightgreen.svg)](https://dependabot.com/) 
[![Commitizen friendly](https://img.shields.io/badge/commitizen-friendly-brightgreen.svg)](http://commitizen.github.io/cz-cli/) 
[![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com) 
[![license](https://img.shields.io/github/license/mashape/apistatus.svg)](https://github.com/wmfs/tymly/blob/master/packages/cardscript-component/LICENSE)


> Cardscript wrapped as a Quasar Component

## <a name="install"></a>Install
```bash
$ npm install @wmfs/cardscript-component --save
```

## <a name="usage"></a>Usage

```
<template>
  <div>
    <cardscript
      :cardscript="example"
      @Submit="onSubmit"
      @Cancel="onCancel"
      @PushCard="onPushCard"
      @InputAddress="onAddress"
      @InputApiLookupParamsChanged="onInputApiLookupParamsChanged"
      @InputApiLookupNext="onInputApiLookupNext"
    />
  </div>
</template>

<script>
import { complex } from '@wmfs/cardscript-examples'
import Cardscript from '@wmfs/cardscript-component'

export default {
  name: 'PageIndex',
  components: { Cardscript },
  data () {
    return {
      example: complex
    }
  },
  methods: {
    onSubmit () {},
    onCancel () {},
    onPushCard () {},
    onAddress () {},
    onInputApiLookupParamsChanged () {},
    onInputApiLookupNext () {}
  }
}
</script>
```

## <a name="license"></a>License
[MIT](https://github.com/wmfs/cardscript-component/blob/master/LICENSE)
