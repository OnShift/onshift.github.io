---
layout: post
authors:
    - dschaefer
date: 2017-10-03 09:00:00 -0500
title: Blog Entry Tutorial
---

A quick tutorial for adding a new blog post on the OnShift Engineering blog.

# Setup a virtual environment

{% highlight bash %}
virtualenv -p python3 ~/.virtualenv/docker
source ~/.virtualenv/docker/bin/activate
{% endhighlight %}

# Fork and clone the repo
{% highlight bash %}
git clone git@github.com:OnShift/onshift.github.io.git -o upstream
{% endhighlight %}

# Create your post
Create: `_posts/{{ page.date | date: '%Y-%m-%d' }}-blog-entry-tutorial.md`
{% highlight yaml %}
---
layout: post
authors:
    - pstar
date: {{ page.date }}
title: Blog Entry Tutorial
---

Add your content here...
{% endhighlight %}

You're Done!

# See your content
{% highlight bash %}
docker-compose up
{% endhighlight %}


# Add your bio
Edit `_data/people.yml`
{% highlight yaml %}
pstar:
  first_name: Patrick
  last_name: Star
  full_name: Patrick Star
  job_title: Bum
  email: ihave5appendages@gmail.com
  avatar: http://images5.fanpop.com/image/photos/25800000/lol-patrick-star-spongebob-25851951-500-500.png
  handles:
    github: pstar
    linkedin: patrickstar
    twitter: Patrick_Star
  bio: |
      Patrick Star is a fictional character in the American animated television series SpongeBob SquarePants. He is voiced by actor Bill Fagerbakke, who also voices numerous other characters on the show. Created and designed by marine biologist and cartoonist Stephen Hillenburg, the series creator, Patrick first appeared on television in the show's pilot episode "Help Wanted" on May 1, 1999.
{% endhighlight %}

# Notes

The blog parses markdown (similar to GitHub markdown) and converts it to HTML. It also uses native HTML.

## References
- [Jekyll Docs](https://jekyllrb.com/docs/home/)
- [Markdown Basics](https://daringfireball.net/projects/markdown/basics)
