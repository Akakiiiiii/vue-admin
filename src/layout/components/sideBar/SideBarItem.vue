<template>
  <div v-if="!item.hidden" class="sideItem">
    <template
      v-if="
        hasOnlyChild(item.children, item) &&
          (!childItem.children || childItem.noChild)
      "
    >
      <page-link v-if="childItem.meta" :to="resolvePath(childItem.path)">
        <el-menu-item :index="resolvePath(childItem.path)">
          <i :class="childItem.meta.icon ? childItem.meta.icon : ''" />
          <span slot="title">{{ childItem.meta.title }}</span>
        </el-menu-item>
      </page-link>
    </template>
    <el-submenu v-else :index="resolvePath(item.path)" popper-append-to-body>
      <template slot="title">
        <i :class="item.meta.icon ? item.meta.icon : ''" />
        <span>{{ item.meta.title }}</span>
      </template>
      <sidebar-item
        v-for="child in item.children"
        :key="child.path"
        :item="child"
        :base-path="item.path"
      />
    </el-submenu>
  </div>
</template>

<script>
import PageLink from './Link'
import { isAbsolutePath } from '@/utils/validate'
import path from 'path'
export default {
  name: 'SidebarItem',
  components: {
    PageLink
  },
  props: {
    item: {
      type: Object,
      required: true
    },
    basePath: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      childItem: null
    }
  },
  methods: {
    hasOnlyChild(children = [], item) {
      // debugger
      const newChildren = children.filter(obj => {
        if (obj.hidden) {
          return false
        } else {
          return true
        }
      })
      if (newChildren.length === 1) {
        this.childItem = newChildren[0]
        return true
      }
      if (newChildren.length === 0) {
        this.childItem = { ...item, noChild: true }
        return true
      }
      return false
    },
    resolvePath(url) {
      if (isAbsolutePath(url)) {
        return url
      }
      if (isAbsolutePath(this.basePath)) {
        return this.basePath
      }
      return path.join(this.basePath, url)
    }
  }
}
</script>
