# django CMS 4.1 documentation 

This is a temporary repo to ramp up django CMS v4 documentation. It is planned to include the contents of this repo in the django-cms repository 
before release  of django CMS 4.1.

Our goal is to create an improved documentation basis which ensures new users of django CMS and experienced programmers a positive experience when 
starting to work with django CMS 4

See the [current status here](https://django-cms-docs.readthedocs.io/en/latest/).


## Status

This repo has started as an empty framework which has been filled step-by-step with django CMS 4 relevant content. Some of it is an adaptation of 
the django CMS 3 documentation some of it is a rewrite. 

Many sections are empty. This is an indication that your contribution is not only valued but also will make a great difference. 

* There is a summary of the [status of key plugin packages](https://django-cms-docs.readthedocs.io/en/latest/explanation/commonly_used_plugins.html).
* Explanation of the new [publishing and versioning system](https://django-cms-docs.readthedocs.io/en/latest/explanation/publishing.html)
* How-to-guides:
  - A rough guideline on [how to upgrade your plugins](https://django-cms-docs.readthedocs.io/en/latest/how_to/10a-upgrade_plugins.html)
  - New way of [using placeholders outside the CMS](https://django-cms-docs.readthedocs.io/en/latest/how_to/02-placeholders.html).
  - New way to [configure applications to work with django CMS](https://django-cms-docs.readthedocs.io/en/latest/reference/app_base.html)
* The [reference part](https://django-cms-docs.readthedocs.io/en/latest/reference/index.html) of the documentation has been largely rewritten.

## Making Pull Requests

Three types of pull requests are more than welcome:

### Adding a section

Mark your pull request with `[new]`. The pull request should add the content of a section or file. 

Do not be afraid to create a PR with a fair first draft for the section. The review process is targeted to generate hints on where to potentially extend, clarify or condense the section. So be prepared to give your PR one or two iterations before it gets merged.

### Improving a section / correcting mistakes

Mark your pull request with `[fix]`. The pull request should correct information, clarify misleading information or add missing information. 

Create this type of PR if you have tested something and found it hard to reproduce, not to work at all, or misleading. 

### Proofreading / typos / clarifications

Mark your pull request with `[clarify]`. These PR improve spelling, grammar, or generally English expression.

Create this PR if you are fluent in English, preferably a native speaker. 

## Raising issues

If you have tried using django CMS 4 and found issues **with the documentation** but cannot propose a change, you are, of course, invited to raise an issue. Issues with respect to the package itself need to be raised in the [django-cms repo](https://github.com/django-cms/django-cms/tree/develop-4). Mark version 4 specific issues with the label `4.x`.

## Trying to figure something out

There is an extensive list of change notes in the [release notes](https://django-cms-docs.readthedocs.io/en/latest/upgrade/4.0.html) which might give you (be it brief) hints on topics not yet covered here.

## The django CMS Association

The django CMS Association is a non-profit organization that was founded in 2020 with the goal to drive the success of django CMS, by increasing customer happiness, market share and open-source contributions. We provide infrastructure and guidance for the django CMS project.

The non-profit django CMS Association is dependent on donations to fulfill its purpose. The best way to donate is to become a member of the association and pay membership fees. The funding will be funneled back into core development and community projects.

[Join the django CMS Association](https://www.django-cms.org/en/contribute/).

