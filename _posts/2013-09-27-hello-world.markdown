---
layout: post
title: "mechanize nokogiri"
date: 2013-09-27 02:06:18
categories: programing co
---

Mechanize and nokogiri to gather some data. mechanize is used for automating interaction with website, automatically stores and sends cookies, follows redirects, and can follow links and submit forms. And  Nokogiri is used for scraping data (HTML).
example : 


{% highlight ruby %}
...................
def initialize
		@agent = Mechanize.new
		agent.user_agent = "Mozilla/5.0 (X11; Linux i686) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/29.0.1547.62 Safari/537.36"
		agent.log = Logger.new(STDOUT)
		agent.get('https://twitter.com/')
end

def set_cookie
		File.open(FILE, 'w') { |f| f.write YAML.dump(@agent.cookie_jar)}
end

def login
		@agent.get('https://twitter.com/login/') do |page|
		@my_page = page.form_with(:action => 'https://twitter.com/sessions') do |f|
			f['session[username_or_email]']		= 'username'
			f['session[password]']						= 'password'
			end.click_button
		end
		set_cookie
end

def get_data
	data = @agent.get('https://twitter.com')
	html = Nokogiri::HTML(data.body)
	print = html.css('.conversations-enabled , .js-original-tweet')
	print.each do |i|
		puts i.css('div.stream-item-header a span').text
		puts i.css('p').text
	end
end
{% endhighlight %}

check link [Mechanize Doc][Mechanize] and [Nokogiri Doc][Nokogiri] ...

[Mechanize]: https://github.com/sparklemotion/mechanize
[Nokogiri]:  http://nokogiri.org/