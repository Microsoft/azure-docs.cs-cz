---
title: Přesouváme image virtuálních počítačů do úložiště spravovaného disku v Azure Marketplace
description: Abychom zajistili rychlejší a spolehlivější úložiště a podporu pro nové funkce a možnosti Marketplace, přesouváme image virtuálních počítačů Marketplace do úložiště spravovaného disku.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
author: kriti-ms
ms.author: krsh
ms.openlocfilehash: e3d20b5e9206cf05d749f75982a07937a8caf078
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201086"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Přesouváme image virtuálních počítačů v Azure Marketplace do úložiště spravovaného disku.

Abychom zajistili rychlejší a spolehlivější úložiště a podporu pro nové funkce a možnosti Marketplace, přesouváme image virtuálních počítačů Marketplace do úložiště spravovaného disku.

Od 2. ledna 2020 se image virtuálního počítače přesunou do úložiště spravovaného disku ve fázích. V první fázi pošleme image jenom bez nových nasazení nebo spuštění virtuálních počítačů za předchozích 90 dnů. Před přesunutím všech imagí pošleme e-mail, aby měl Vydavatel informace o tom, které obrázky se přesunou a kdy se budou přesouvat.

Vydavatelé nebo spotřebitelé nepotřebují provádět žádnou akci a uživatelé to nebudou mít vliv. Nabídky na webu Marketplace zůstanou dostupné a zákazníci pořád budou moct nasadit spravované virtuální počítače z těchto imagí během a po přesunutí.

Pokud máte nějaké dotazy, [kontaktujte nás](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)prosím.

## <a name="faqs"></a>Nejčastější dotazy

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>Dochází k výpadku vašich uživatelských imagí virtuálních počítačů?

Uživatelé imagí virtuálních počítačů nebudou mít žádný výpadek. 

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Jak dlouho trvá dokončení procesu?

Dokončení migrace může trvat až 24 hodin.

### <a name="do-i-need-to-take-any-action"></a>Potřebuji nějakou akci?

No. Vydavatelé nebo spotřebitelé nemusí provádět žádné akce.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>Musím po přesunu do spravovaného diskového úložiště aktualizovat systém rozhraní API cloudového portálu jiným způsobem.

No. Vaše stávající volání rozhraní API budou fungovat i nadále.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Budou všechny image virtuálních počítačů přesunuty do spravovaného disku ve stejnou dobu?

Všechny image virtuálních počítačů budeme měsíčně přemísťovat na stejný den. Po přesunutí vám pošleme zprávu.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>Můžu si vyžádat, aby se přesun imagí virtuálních počítačů naplánoval na pozdější dobu?

Doporučujeme přemístit obrázky v naplánovaném datu. Pokud máte ale obavy, obraťte se na nás a naplánujte přesun.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>Můžu během přesunu publikovat aktualizace imagí virtuálních počítačů?

Během přesouvání nelze provést aktualizace imagí virtuálních počítačů.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>Změní se proces publikování po přesunu image virtuálního počítače na spravovaný disk?

Ne, proces publikování zůstane stejný. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>Může vydavatel přesunout své nabídky na spravovaný disk?

Ne, Vydavatelé nemůžou přesunout své nabídky na spravovaný disk. Budou muset počkat a jejich obrázky budou automaticky přesunuty. Než provedeme změny, pošleme vám oznámení na vydavatele.
