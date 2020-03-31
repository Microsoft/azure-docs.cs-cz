---
title: Přesouváme image virtuálních počítačů (VM) do spravovaného diskového úložiště na Azure Marketplace.
description: Abychom zajistili rychlejší a spolehlivější úložiště a podporu pro nové funkce a funkce marketplace, přesouváme image virtuálních počítačů marketplace do spravovaného úložiště disků.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 17b418ab82cc88b4368efe247b06c0c8bbb27453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285108"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Přesouváme image virtuálních počítačů (VM) na Azure Marketplace do spravovaného diskového úložiště.

Abychom zajistili rychlejší a spolehlivější úložiště a podporu pro nové funkce a funkce marketplace, přesouváme image virtuálních počítačů marketplace do spravovaného úložiště disků.

ledna 2020 přesuneme image virtuálních počítačů do spravovaného úložiště disků ve fázích. V první fázi budeme přesouvat jenom image bez nových nasazení nebo spuštěných virtuálních počítače v předchozích 90 dnech. Než přesuneme některý z obrázků, pošleme vydavateli e-mail s tím, že vydavatel bude vědět, které obrázky se budou pohybovat a kdy budou přesunuty.

Vydavatelé ani spotřebitelé nemusí provádět žádnou akci a uživatelé nebudou ovlivněni. Nabídky marketplace zůstanou k dispozici a zákazníci budou stále moct nasadit spravované virtuální virtuální aplikace z těchto iblancí během přesunu a po něm.

Máte-li jakékoli dotazy, [kontaktujte nás](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff).

## <a name="faqs"></a>Nejčastější dotazy

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>Dojde k výpadku uživatelů mých bitových kopií virtuálních počítačů?

Uživatelům bitových kopií virtuálního počítačů nedochází k výpadku. 

V první fázi budeme pouze přesunout image virtuálních mích, které nemají spuštěné virtuální chod. Vzhledem k tomu, že pro tyto obrázky nejsou žádní uživatelé, nebude to mít žádný dopad. I pro následující fáze nebude mít na uživatele žádný dopad.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Jak dlouho trvá dokončení procesu?

Dokončení migrace může trvat až 24 hodin.

### <a name="do-i-need-to-take-any-action"></a>Musím něco podniknout?

Ne. Vydavatelé ani spotřebitelé nemusí provádět žádnou akci.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>Je třeba aktualizovat svůj systém tak, aby po přesunutí do úložiště spravovaného disku volal a volal na api portálu Cloud jiným způsobem?

Ne. Vaše stávající volání rozhraní API budou nadále fungovat.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Byly by všechny moje image virtuálního počítače přesunuty na spravovaný disk současně?

Budeme přesouvat všechny vaše obrázky virtuálních počítačí ve stejný den. Jakmile budou přemístěny, upozorníme vás.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>Můžu požádat o naplánování přesunu obrázků virtuálních virtuálních<koutů na pozdější dobu?

Doporučujeme přesunout obrázky v naplánované datum. Nicméně, pokud máte obavy, prosím, kontaktujte nás přeplánovat tah.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>Můžu během přesunu publikovat aktualizace obrázků virtuálních aplikací?

Aktualizace image virtuálního mísa nelze provést během přesunutí.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>Změní se proces publikování po přesunutí image virtuálního počítače na spravovaný disk?

Ne, proces publikování zůstane stejný. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>Může vydavatel přesunout své nabídky na spravovaný disk?

Ne, vydavatelé nemohou přesunout své nabídky na spravovaný disk. Budou muset počkat a jejich obrázky budou přesunuty automaticky. Před prováděním jakýchkoli změn budeme vydavateli zasílat oznámení.
