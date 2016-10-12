---
layout: post
status: publish
published: true
title: Ploop snapshot
author: m_messiah
author_email: m.muzafarov@gmail.com
author_url: https://m-messiah.com
date: 2016-10-12 09:55:00.000000000 +03:00
categories: linux
tags: []
comments: true
---

# Проблема

Столкнулся с проблемой создания snapshot в ploop:

```bash
Creating snapshot {$ID}
Storing /vz/private/ploop/$VEID/Snapshots.xml.tmp
Error in is_old_snapshot_format (snapshot.c:39): Snapshot is in old format
Failed to create snapshot: Error in is_old_snapshot_format (snapshot.c:39): Snapshot is in old format [38]
Failed to create snapshot
```

В интернете по этому поводу ничего не видать, поэтому пишу свои причины и решение.

# Причина:

Базовый ploop снапшот создан в предыдущей версии, после чего ploop в системе обновился, а снапшот остался прежним.

# Решение:

Конвертировать ploop не имеет смысла, потому что базовый снапшот остается тем же. Но помогает любая операция по изменению состояния ploop диска, например изменение размера в ту или иную сторону.
В частности:

+ для openvz6: `vzctl set $VEID --diskspace $SIZE --save`
+ для ploop: `ploop resize -s $SIZE DiskDescriptor.xml`
