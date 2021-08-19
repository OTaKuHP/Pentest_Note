# Recon suites review

## Intro

**What?** This is a December 2020 hunting/pentesting recon suites review made by myself. I have compared and review every tool one by one and obtained a general view of the "state-of-the-art" of the most used recon tools.

**Why?** Lately there has been an explosion in the creation of these types of tools, and I was simply curious about how each one faced the challenge of profiling one or more objectives.

**How?** First, I have analyzed what features the suites have and then what tools they used to achieve those functionalities.

From my POV a recon tool should get as much information as possible from a target regardless of its size. From subdomains enumeration to analyze all JS and their possible secrets, through SSL failures or consult information in public sources. Neither am I looking for a tool that will get all the low-hanging fruit for P1 automatically continuously, let's be honest, most people are looking for this, and you don't have the necessary to set up a competent infrastructure to achieve it.

I thought about making measurements on the number of subdomains that each tool retrieves and the number of information that they retrieve in general, but this poses several problems. In the end, these suites launch existing subdomain enumeration tools, so I'll do that other day \(spoiler! 😲\) and it doesn't really depend on the suite itself. On the other hand, each tool does different processes with different tools, so it would not be fair \(or measurable, I think\) to make a comparison of the quantity or quality of information they obtain.

My perfect recon suite should be able to do the following: run a command, review its contents, and then run another tool with that information, like "subdomain enum \| httpx \| gf \| dalfox". Yeah I know, it's a simple oneliner, but also, I want a lot of different checks in an easy readable and organized way. Easy? Let's see.

{% hint style="info" %}
This is not intended to be a serious investigation, a technical paper, or anything like that, just a series of tests that I have done for fun. The results shown are my opinion and if at any time you don't like them, or you don't agree, you can stop reading or explain to me how I could have done it better 😉
{% endhint %}

All the results of my runs and tests are posted [here](https://docs.google.com/spreadsheets/d/1XVi9eKWvVZw9zrX46XEZD3LfiEMRCg327hOn4AbTZWs/edit?usp=sharing), it has three sheets \(Summary, features and tools\).

{% embed url="https://docs.google.com/spreadsheets/d/1XVi9eKWvVZw9zrX46XEZD3LfiEMRCg327hOn4AbTZWs/edit?usp=sharing" caption="" %}

## Tools

Small summary of each tool with the features and results that I got. This section not follows any special order.

### [Bheem](https://github.com/harsh-bothra/Bheem)

![](../.gitbook/assets/image%20%2838%29.png)

* Author: [harsh-bothra](https://github.com/harsh-bothra) & [KathanP19](https://github.com/KathanP19)
* Language: Bash

It's composed of a lot of simple bash scripts that are calling each other which makes it much easier to add some changes that fit for you or what you want or add your own.

#### Pros

* Superb workflow.
* Easy to understand and adapt for your needs.
* Best and trendy tools like nuclei, dalfox or gf patterns.
* Scope defined workflows.

#### Cons

* No web screenshots.
* Lack of output customization.

### [3klcon](https://github.com/eslam3kl/3klCon)

![](../.gitbook/assets/image%20%2834%29.png)

* Author: [eslam3kl](https://github.com/eslam3kl)
* Language: Python2

This tool continues the process of the author's tool [3klector](https://github.com/eslam3kl/3klector) and have a strong workflow which covers a lot of things.

#### Pros

* ASN and acquisitions collector.
* Provides Dorks to check manually.

#### Cons

* Python2 died a year ago, too much for a live project imho.
* No subdomain bruteforce.
* No web screenshots.

### [Sudomy](https://github.com/Screetsec/Sudomy)

![](../.gitbook/assets/image%20%2843%29.png)

* Author: [Screetsec](https://github.com/Screetsec)
* Language: Python3

I have been using this tool for a lot of time, It does a very good job of enumerating subdomains giving complete results.

#### Pros

* Uses Shodan for fast port scan.
* Vhosts checker.
* Wordlist generator from target.
* Slack notifications.

#### Cons

* Needs API keys.
* No vulns scanner.
* No endpoints checks like xss, params, js, etc.

### [Osmedeus](https://github.com/j3ssie/Osmedeus)

![](../.gitbook/assets/image%20%2839%29.png)

* Author: [j3ssie](https://github.com/j3ssie)
* Language: Python3

One of the well known, in a short time it has become one of the best known, now its author is evolving this project in huntersuite.io \(paid\).

#### Pros

* Web interface.
* Nice report output.
* Slack notifications.
* ffuf for fuzzing.

#### Cons

* No WAF checker
* Jaeles for vulns scan feels buggy.
* No endpoints analysis like potential xss, params, js, etc.

### [FinalRecon](https://github.com/thewhiteh4t/FinalRecon)

![](../.gitbook/assets/image%20%2837%29.png)

* Author: [thewhiteh4t](https://github.com/thewhiteh4t)
* Language: Python3

Recently added to the official Kali repositories, increasingly known and used. Mainly focused on web scan, but it does the recon phase too.

#### Pros

* Very good cli output.
* Customizable files output.
* Not use external tools, does almost everything by its own.

#### Cons

* Need API keys.
* Only passive subdomain enumeration.
* Lack of features surprisingly.

### [reNgine](https://github.com/yogeshojha/rengine)

![](../.gitbook/assets/image%20%2830%29.png)

* Author: [yogeshojha](https://github.com/yogeshojha)
* Language: Python3

A tool driven by a web interface \(only\) with a good integration of the best tools such as amass, nuclei or dirsearch.

#### Pros

* Web interface.
* Customizable files output.
* Schedule feature and dashboard.
* Exclude subdomains feature.

#### Cons

* No cli output.
* No subdomains permutations or bruteforce.
* Displaying directory enumeration in web interface is not good at all.

### [Rock-ON](https://github.com/SilverPoision/Rock-ON)

![](../.gitbook/assets/image%20%2831%29.png)

* Author: [SilverPoision](https://github.com/SilverPoision)
* Language: Bash

This tool has not been updated for more than a year but anyway it does it works really well, not much features but good implemented.

#### Pros

* ASN enumeration.
* Vhosts detection.
* Slack integration.

#### Cons

* API keys needed.
* No endpoints analysis like potential xss, params, js, etc.

### [recon-pipeline](https://github.com/epi052/recon-pipeline)

![](../.gitbook/assets/image%20%2842%29.png)

* Author: [epi052](https://github.com/epi052)
* Language: Python3

This is a total different approach from the others. In this tool you have to define a recon pipeline or use one of previously defined, maybe needs more learning curve \(but good [docs](https://recon-pipeline.readthedocs.io/)\) but totally customizable.

#### Pros

* PIpeline customizable definition.
* Absolutely customizable approach.
* Scheduler.

#### Cons

* Searchsploit for vulns detection.
* No endpoints analysis like potential xss, params, js, etc.

### [OneForAll](https://github.com/shmilylty/OneForAll)

![](../.gitbook/assets/image%20%2840%29.png)

* Author: [shmilylty](https://github.com/shmilylty)
* Language: Python3

‌I didn't know anything about this tool but it's really famous \(almost 3K stars\) and that's because it uses almost every API that exists to give one of the best passive scan experience thtat exists for now.

#### Pros‌ <a id="pros-7"></a>

* More than 40 API keys integration.
* Zone transfer checker.
* Scheduler.

#### Cons <a id="cons-7"></a>

* Searchsploit for vulns detection.
* No endpoints analysis like potential xss, params, js, etc.

### [chomp-scan](https://github.com/SolomonSklash/chomp-scan)

![](../.gitbook/assets/image%20%2828%29.png)

* Author: [SolomonSklash](https://github.com/SolomonSklash)
* Language: Bash

‌I have been using this tool for a long time during my pentests and I like it very much. It's a scripted bash pipeline with a lot of tests.

#### Pros‌ <a id="pros-7"></a>

* Really good cli output.
* CORS specific checks.
* ffuf for fuzzing.

#### Cons <a id="cons-7"></a>

* Nikto for web vulns.
* Notica for notifications.

### [ReconPi](https://github.com/x1mdev/ReconPi)

![](../.gitbook/assets/image%20%2832%29.png)

* Author: [x1mdev](https://github.com/x1mdev)
* Language: Bash

Nice all-in-one installer designed to start the recon process in a low hardware device like Raspberry Pi in a lightweight way.

#### Pros‌ <a id="pros-7"></a>

* Best and trendy tools like nuclei, dalfox or gf patterns.
* Slack and Discord notifications.
* Lot of passive subdomains tools included.

#### Cons <a id="cons-7"></a>

* Need API keys.
* Installer install tools not used in the script.

### [HydraRecon](https://github.com/aufzayed/hydrarecon)

![](../.gitbook/assets/image%20%2841%29.png)

* Author: [aufzayed](https://github.com/aufzayed)
* Language: Python3

Little known tool that does the whole recognition process in a custom way.

#### Pros‌ <a id="pros-7"></a>

* JS extractor.
* No use 3rd parties tools.

#### Cons <a id="cons-7"></a>

* Lack of features.
* No endpoints analysis like potential xss, params, js, etc.

### [lazyrecon](https://github.com/nahamsec/lazyrecon)

![](../.gitbook/assets/image%20%2835%29.png)

* Author: [nahamsec](https://github.com/nahamsec)
* Language: Bash

Well known tool created by one of the big guys. It does the work in a fast an easy way and create a pretty html report easy to review.

#### Pros‌ <a id="pros-7"></a>

* Exclude subdomains feature.
* Wordlist generation.

#### Cons <a id="cons-7"></a>

* No vulns/tech scanner.
* No endpoints analysis like potential xss, params, js, etc.

### [Sn1per](https://github.com/1N3/Sn1per)

![](../.gitbook/assets/image%20%2829%29.png)

* Author: [1N3](https://github.com/1N3)
* Language: Bash

This is an All-In-One hacking tool but apart from this, also have a good recon capabilities that performs almost everything.

#### Pros‌ <a id="pros-7"></a>

* ASN enumeration.
* Transfer zone, vhosts and and waf checks.
* Most complete in features tool.

#### Cons <a id="cons-7"></a>

* Too heavy to do recon \(docker image &gt; 6 GB\).
* No endpoints analysis like potential xss, params, etc.

### [Rapidscan](https://github.com/skavngr/rapidscan)

![](../.gitbook/assets/image%20%2833%29.png)

* Author: [skavngr](https://github.com/skavngr)
* Language: Python2

I have been using this tool some time ago because it provides an easy human-readable output, with suggestions, good workflow and ETA in every step.

#### Pros‌ <a id="pros-7"></a>

* Really nice cli output results.
* Suggests resolution for each bug found.
* Transfer zone

#### Cons <a id="cons-7"></a>

* Oldie tools like nikto, uniscan.
* Python2 died a year ago, too much for a live project imho.

## Results

### Features

1. Sn1per
2. Sudomy
3. Bheem & osmedeus
4. ReconPi & ChompScan
5. Rapidscan & lazyrecon & 3klcon

### Workflow and usage

1. Bheem
2. ReconPi
3. Osmedeus & 3klcon
4. Sudomy
5. rapidscan & chompscan

### General

1. Bheem
2. ReconPi
3. Sn1per & osmedeus
4. Sudomy & 3klcon
5. rapidscan & chompscan

## Final thoughts

I was very surprised by the amount of very good tools that exist for recognition, I have discovered many very good tools that I did not know and others I have been able to understand better how they work. I was also surprised by how quickly nuclei has established itself as one of the best tools that exist in the panorama and on the other hand, that dorking is no longer used, even if it is simply to return a list of urls to check manually. **Bheem** seems to me to be the best tool that adapts to my work methodology and I hope they continue to maintain and update it because it does the job very well.

Finally, thanks to all the tool developers who facilitate our work and implement the recon methodology better and better.

