<template> 
  <main class="flex min-h-screen flex-col bg-muted/30">
    <header class="fixed inset-x-0 top-0 z-50 border-b bg-background/95 backdrop-blur">
      <div class="mx-auto flex min-h-16 max-w-6xl items-center justify-between gap-4 px-5">
        <a href="/" class="flex min-w-0 items-center gap-2 font-semibold">
          <img :src="brandLogoUrl" :alt="`${site.name} Logo`" class="size-9 shrink-0 rounded-md object-contain" />
          <span class="truncate">{{ site.name }}</span>
        </a>
        <div class="flex shrink-0 items-center gap-2">
          <PublicNav />
        </div>
      </div>
    </header>

    <section class="border-b bg-background pt-16">
      <div class="mx-auto grid max-w-6xl gap-5 px-5 py-8 sm:grid-cols-[minmax(0,1fr)_22rem] sm:items-center">
        <div v-if="site.notice" class="flex min-w-0 items-center gap-3 text-sm leading-6 text-muted-foreground">
          <InfoIcon class="size-4 shrink-0 text-foreground" />
          <div class="min-w-0 max-w-[28.8rem] overflow-hidden whitespace-nowrap">
            <p v-if="!shouldMarqueeNotice" class="m-0 truncate" :title="site.notice">{{ site.notice }}</p>
            <template v-else>
              <p class="sr-only">{{ site.notice }}</p>
              <div aria-hidden="true" class="notice-marquee-track inline-flex w-max animate-notice-marquee">
                <span class="shrink-0 pr-12">{{ site.notice }}</span>
                <span class="shrink-0 pr-12">{{ site.notice }}</span>
              </div>
            </template>
          </div>
        </div>
        <p v-else class="text-sm text-muted-foreground">{{ site.subtitle || messages.storefront.fallbackSubtitle }}</p>
        <div class="relative">
          <SearchIcon class="pointer-events-none absolute left-3 top-1/2 size-4 -translate-y-1/2 text-muted-foreground" />
          <Input v-model="query" class="pl-9" :placeholder="messages.storefront.searchProducts" :aria-label="messages.storefront.searchProducts" />
        </div>
      </div>
    </section>

    <section class="mx-auto w-full max-w-6xl flex-1 px-5 py-8 sm:py-10">
      <div class="flex flex-col gap-6 md:flex-row">
        <aside class="md:w-48 md:shrink-0">
          <h3 class="mb-3 hidden text-sm font-semibold text-muted-foreground md:block">{{ messages.storefront.categories }}</h3>
          <div class="flex gap-2 overflow-x-auto pb-1 md:flex-col md:gap-1 md:overflow-visible md:pb-0">
            <Button size="sm" class="shrink-0 justify-start md:w-full" :variant="selectedCategory === null ? 'default' : 'ghost'" @click="selectedCategory = null">
              {{ messages.storefront.all }}
            </Button>
            <Button v-for="category in data.categories" :key="category.id" size="sm" class="shrink-0 justify-start md:w-full" :variant="selectedCategory === category.id ? 'default' : 'ghost'" @click="selectedCategory = category.id">
              {{ category.name }}
            </Button>
          </div>
        </aside>

        <div class="min-w-0 flex-1">
          <div class="mb-4 flex items-end justify-between gap-4">
            <div>
              <h2 class="text-xl font-semibold tracking-normal">{{ messages.storefront.products }}</h2>
              <p class="mt-1 text-sm text-muted-foreground">{{ query ? t(messages.storefront.relatedProducts, { count: visibleProducts.length }) : messages.storefront.fallbackSubtitle }}</p>
            </div>
          </div>
          <div v-if="visibleProducts.length" class="grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
            <article v-for="product in visibleProducts" :key="product.id" class="group overflow-hidden rounded-lg border bg-card transition-shadow hover:shadow-lg">
              <a :href="`/product/${product.slug}`" class="block">
                <div class="relative aspect-4/3 overflow-hidden bg-muted">
                  <img :src="product.coverImage || defaultProductImage" :alt="product.name" class="size-full object-cover transition-transform duration-300 group-hover:scale-105" />
                  <Badge variant="secondary" class="absolute left-2 top-2 bg-background/90 text-xs backdrop-blur">{{ product.categoryName || messages.storefront.products }}</Badge>
                </div>
                <div class="flex min-h-28 flex-col p-3">
                  <h3 class="line-clamp-2 text-sm font-semibold tracking-normal">{{ product.name }}</h3>
                  <p v-if="product.subtitle" class="mt-1 line-clamp-1 text-xs text-muted-foreground">{{ product.subtitle }}</p>
                  <div class="mt-auto flex items-end justify-between gap-2 pt-3">
                    <span :class="stockClass(product)" class="text-xs">{{ stockLabel(product) }}</span>
                    <span class="shrink-0 text-lg font-semibold tabular-nums">¥{{ product.price }}</span>
                  </div>
                </div>
              </a>
            </article>
          </div>
          <div v-else class="border border-dashed bg-background px-6 py-16 text-center">
            <PackageOpenIcon class="mx-auto size-8 text-muted-foreground" />
            <h3 class="mt-4 text-base font-semibold">{{ messages.storefront.noProducts }}</h3>
            <p class="mt-2 text-sm text-muted-foreground">{{ messages.storefront.noProductsDescription }}</p>
          </div>
        </div>
      </div>
    </section>
    <StorefrontFooter />
  </main>
</template>
