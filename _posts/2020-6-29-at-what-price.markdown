---
layout: post
title: "At What Price?"
subtitle: "The Point When Testing Efforts Destroy Your Hard Work"
date: 2020-6-29 13:37:00
categories: testing
---
Most of you have experienced such a situation where testing feels right but doing so is inconvenient. I'm not talking testing in general but rather about code that should've been tested differently.


Unergonomic.

{% highlight kotlin linenos %}
fun Context.overrideLocale(
  sharedPrefs: SharedPrefs = SharedPrefs.instance(this),
  languageProvider: LanguageProvider = LanguageProvider(sharedPrefs),
  setDefaultLocale: SetDefaultLocale = Locale::setDefault,
  configurationFactory: ConfigurationFactory = ::Configuration
): Context {
  val locale = createNewLocale(languageProvider)
  setDefaultLocale(locale)
  val config = configurationFactory(this.resources.configuration)
      .apply { setLocale(locale) }
  return this.createConfigurationContext(config)
}
{% endhighlight %}

{% highlight kotlin linenos %}
fun Context.overrideLocale(): Context {
  val sharedPrefs = SharedPrefs.instance(this)
  val languageProvider = LanguageProvider(sharedPrefs)
  val locale = createNewLocale(languageProvider)
  Locale.setDefaultLocale(locale)
  val config = Configuration(this.resources.configuration)
      .apply { setLocale(locale) }
  return this.createConfigurationContext(config)
}
{% endhighlight %}
