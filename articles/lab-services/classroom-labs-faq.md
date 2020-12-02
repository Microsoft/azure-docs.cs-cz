---
title: Laboratoře v Azure Lab Services – nejčastější dotazy | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Labs v Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 757af8f30e9a71a3889d9f625c87a002af2e1302
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437180"
---
# <a name="labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Cvičení v Azure Lab Services – nejčastější dotazy (FAQ)
Získejte odpovědi na některé z nejběžnějších otázek o Labs v Azure Lab Services. 

## <a name="quotas"></a>Kvóty

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Je kvóta na uživatele nebo za týden nebo na celou dobu trvání testovacího prostředí? 
Kvóta, kterou nastavíte pro testovací prostředí, je určena pro každého studenta po celou dobu trvání testovacího prostředí. A [plánovaná doba spuštění virtuálních počítačů](how-to-create-schedules.md) se nepočítá s kvótou přidělenou uživateli. Kvóta je určena pro dobu mimo plánované hodiny, kterou student stráví na virtuálních počítačích.  Další informace o kvótách najdete v tématu [nastavení kvót pro uživatele](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="if-educator-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Pokud Educator zapne virtuální počítač studenta, má vliv na kvótu studenta? 
Ne. Žádnou. Když se Educator zapne na virtuálním počítači studenta, neovlivní to kvótu, která je přidělena studentovi. 

## <a name="schedules"></a>Plány

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Spustí se všechny virtuální počítače v testovacím prostředí automaticky při nastavení plánu? 
Ne. Ne všechny virtuální počítače. Pouze virtuální počítače, které jsou přiřazeny uživatelům podle plánu. Virtuální počítače, které nejsou přiřazeny uživateli, se automaticky nespustí. Je to záměrné. 

## <a name="lab-accounts"></a>Účty testovacího prostředí

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Proč není možné vytvořit testovací prostředí z důvodu nedostupnosti rozsahu adres? 

Laboratoře můžou vytvářet testovací virtuální počítače v rámci rozsahu IP adres, který zadáte při vytváření účtu testovacího prostředí v Azure Portal. Když je poskytnutý rozsah adres, každé testovací prostředí, které se vytvoří po přidělené IP adrese pro virtuální počítače v testovacím prostředí, se 512. Rozsah adres pro účet testovacího prostředí musí být dostatečně velký, aby vyhovoval všem laboratořím, které v rámci účtu testovacího prostředí máte v úmyslu vytvořit. 

Například pokud máte blok z/19-10.0.0.0/19, tento rozsah adres bude vyhovovat 8192 IP adres a 16 Labs (8192/512 = 16 Labs). V tomto případě se vytvoření testovacího prostředí v testovacím prostředí 17 nezdařilo.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Jaké rozsahy portů mám otevřít v nastavení brány firewall mojí organizace pro připojení k virtuálním počítačům testovacího prostředí přes RDP/SSH?

Porty jsou: 49152 – 65535. Labs za nástrojem pro vyrovnávání zatížení. Každé testovací prostředí má jednu veřejnou IP adresu a každý virtuální počítač v testovacím prostředí má jedinečný port. 

Na kartě **fond virtuálních počítačů** na domovské stránce domovské stránky testovacího prostředí v Azure Portal můžete zobrazit také soukromou IP adresu každého virtuálního počítače. Pokud znovu publikujete testovací prostředí, veřejná IP adresa testovacího prostředí se nemění, ale privátní IP adresa a číslo portu každého virtuálního počítače v testovacím prostředí se můžou změnit. Další informace najdete v článku [nastavení brány firewall pro Azure Lab Services](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Jaký rozsah veřejných IP adres mám otevřít v nastavení brány firewall moje organizace pro připojení k testovacím virtuálním počítačům přes RDP/SSH?
Přečtěte si téma [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519), který poskytuje rozsah veřejných IP adres pro datová centra v Azure. Můžete otevřít IP adresy pro oblasti, ve kterých jsou účty testovacího prostředí.

## <a name="virtual-machine-images"></a>Image virtuálních počítačů

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Proč při vytváření nového testovacího prostředí nemůžete v rozevíracím seznamu image virtuálních počítačů povolit další možnosti imagí, proč je autor testovacího prostředí?

Když vám správce přidá jako tvůrce testovacího prostředí do účtu testovacího prostředí, budete mít oprávnění k vytváření cvičení. Nemáte ale oprávnění upravovat žádná nastavení v rámci účtu testovacího prostředí, včetně seznamu povolených imagí virtuálních počítačů. Pokud chcete povolit další image, požádejte správce účtu testovacího prostředí, aby to prohlédl za vás, nebo požádejte správce, aby vás přidal jako roli přispěvatele k účtu testovacího prostředí. Role Přispěvatel vám poskytne oprávnění k úpravám seznamu imagí virtuálního počítače v účtu testovacího prostředí.

### <a name="can-i-attach-additional-disks-to-a-virtual-machine"></a>Můžu k virtuálnímu počítači připojit další disky?
Ne. k virtuálnímu počítači v testovacím prostředí učebny není možné připojit další disky. 

## <a name="users"></a>Uživatelé

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Kolik uživatelů může být v prostředí učebny?
Do testovacího prostředí učebny můžete přidat až 400 uživatelů. 

## <a name="blog-post"></a>Příspěvek na blogu
Přihlaste se k odběru [blogu Azure Lab Services](https://aka.ms/azlabs-blog).

## <a name="update-notifications"></a>Aktualizovat oznámení
Přihlaste se k odběru [aktualizací testovacích služeb](https://azure.microsoft.com/updates/?product=lab-services) a udržujte si informace o nových funkcích v testovacích službách.

## <a name="general"></a>Obecné
### <a name="what-if-my-question-isnt-answered-here"></a>Co když tady není odpověď na moji otázku?
Pokud tady uvedený dotaz není, sdělte nám, abychom vám pomohli najít odpověď.

- Na konci těchto nejčastějších dotazů si vystavte otázku. 
- Pokud chcete oslovit širší cílovou skupinu, odešlete dotaz na [Azure Lab Services – fórum pro pracovníky odborné komunity](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices). 
- V případě žádostí o funkce odešlete žádosti a nápady do [Azure Lab Services – uživatelský hlas](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

