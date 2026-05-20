source "https://rubygems.org"

# Jekyll 4.x — no theme dependency. The site uses its own minimal layouts
# in _layouts/ and Tailwind CSS via CDN for styling. See plan.md for the
# rationale (path C resolution, 2026-05-19).
gem "jekyll", "~> 4.3"

group :jekyll_plugins do
  gem "jekyll-sitemap", "~> 1.4"       # FR-MKT-072
  gem "jekyll-seo-tag", "~> 2.8"       # structured-data helpers; NFR-MKT-005
end

# Windows / JRuby compatibility — keeping for portability even though
# CI runs on Ubuntu.
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", "~> 2.0"
  gem "tzinfo-data"
end

gem "wdm", "~> 0.1.1", :install_if => Gem.win_platform?
