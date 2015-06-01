---
layout: post
title: LibreOffice crashes on startup
---
<p>For anyone who has suddenly found that LibreOffice crashes during startup (no error message) and, like me, couldn’t figure it out for ages: LibreOffice >3.3 isn’t able to detect Java Runtime Environment version 1.7.x, so if you upgrade Java don’t uninstall the 1.6.x version.
<p>There’s a <a href="https://bugs.freedesktop.org/show_bug.cgi?id=39659">bug report</a>. It’s going to annoy a lot of people when Java 1.7.x goes mainstream.
<p><strong>Edit (4<sup>th</sup> January 2012):</strong> This issue has <a href="https://bugs.freedesktop.org/show_bug.cgi?id=39659#c46">apparently been fixed</a> in LibreOffice 3.4.5 (which is currently in beta, and due to be released before the end of the month).