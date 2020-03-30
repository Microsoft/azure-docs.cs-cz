---
title: Učebny ve službách Azure Lab Services – nejčastější dotazy | Dokumenty společnosti Microsoft
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se testovacích prostředí pro učebny ve službě Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 8d1ed128181d036af0026ae273c2c5bf1d3a066e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443495"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Učebny ve službách Azure Lab Services – nejčastější dotazy (nejčastější dotazy)
Získejte odpovědi na některé nejčastější dotazy týkající se testovacích prostředí ve třídě ve službě Azure Lab Services. 

## <a name="quotas"></a>Kvóty

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Je kvóta na uživatele nebo za týden nebo za celou dobu trvání testovacího prostředí? 
Kvóta nastavená pro testovací prostředí je pro každého studenta po celou dobu trvání laboratoře. A [naplánovaná doba spuštění virtuálních disponecí](how-to-create-schedules.md) se nezapočítává do kvóty přidělené uživateli. Kvóta je pro čas mimo plánované hodiny, které student stráví na virtuálních počítačích.  Další informace o kvótách naleznete v tématu [Nastavení kvót pro uživatele](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="if-professor-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Pokud profesor zapne studentský virtuální počítač, ovlivní to kvótu pro studenty? 
Ne. To není. Když profesor zapne virtuální počítač studenta, nemá to vliv na kvótu přidělenou studentovi. 

## <a name="schedules"></a>Plány

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Spouštějí se všechny virtuální aplikace v testovacím prostředí automaticky, když je nastaven plán? 
Ne. Ne všechny virtuální mě. Pouze virtuální chody, které jsou přiřazeny uživatelům podle plánu. Virtuální aplikace, které nejsou přiřazeny uživateli, se automaticky nespustí. Je to záměrné. 

## <a name="lab-accounts"></a>Účty testovacího prostředí

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Proč nelze vytvořit testovací prostředí z důvodu nedostupnosti rozsahu adres? 
Učebna labs můžete vytvořit virtuální počítače testovacího prostředí v rozsahu IP adres, které zadáte při vytváření účtu testovacího prostředí na webu Azure Portal. Když je k dispozici rozsah adres, každé testovací prostředí, které se vytvoří po přidělené 512 IP adresy pro virtuální chod testovacího prostředí. Rozsah adres pro účet testovacího prostředí musí být dostatečně velký, aby vyhovoval všem testovacím prostředím, které chcete vytvořit pod účtem testovacího prostředí. 

Například pokud máte blok /19 - 10.0.0.0/19, tento rozsah adres pojme 8192 IP adresy a 16 labs (8192/512 = 16 labs). V tomto případě se nezdaří vytvoření testovacího prostředí při vytvoření 17.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Jaké rozsahy portů mám otevřít v nastavení brány firewall organizace pro připojení k virtuálním počítačům Lab přes RDP/SSH?

Přístavy jsou: 49152–65535. Učebna laboratoře sedět za nástroj pro vyrovnávání zatížení. Každé testovací prostředí má jednu veřejnou IP adresu a každý virtuální počítač v testovacím prostředí má jedinečný port. 

Můžete také zobrazit privátní IP adresu každého virtuálního počítače na kartě **Fond virtuálních počítačů** na domovské stránce pro testovací prostředí na webu Azure Portal. Pokud znovu publikujete testovací prostředí, veřejná IP adresa testovacího prostředí se nezmění, ale soukromé IP a číslo portu každého virtuálního počítače v testovacím prostředí se může změnit. Další informace najdete v článku: [Nastavení brány firewall pro Azure Lab Services](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Jaký rozsah veřejných IP adres mám otevřít v nastavení brány firewall organizace pro připojení k virtuálním počítačům Lab přes RDP/SSH?
Viz [Rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519), který poskytuje rozsah veřejných IP adres pro datová centra v Azure. Můžete otevřít IP adresy pro oblasti, kde jsou vaše účty v testovacím prostředí.

## <a name="virtual-machine-images"></a>Image virtuálních strojů

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Proč jako tvůrce testovacího prostředí nemohu povolit další možnosti bitových kopií v rozevíracím seznamu image virtuálních strojů při vytváření nového testovacího prostředí?

Když vás správce přidá jako tvůrce testovacího prostředí do účtu testovacího prostředí, budete mít oprávnění k vytváření testovacích prostředí. Ale nemáte oprávnění k úpravám nastavení uvnitř účtu testovacího prostředí, včetně seznamu povolených bitových kopií virtuálních strojů. Chcete-li povolit další obrázky, obraťte se na správce účtu testovacího prostředí, který to udělá za vás, nebo požádejte správce, aby vás přidal jako roli přispěvatele do účtu testovacího prostředí. Role Přispěvatel vám poskytne oprávnění k úpravám seznamu bitových obrázků virtuálního počítače v účtu testovacího prostředí.

## <a name="users"></a>Uživatelé

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Kolik uživatelů může být v učebně laboratoři?
Do učební laboratoře můžete přidat až 400 uživatelů. 

## <a name="blog-post"></a>Blogu
Přihlaste se k [odběru blogu Azure Lab Services](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Aktualizovat oznámení
Přihlaste se k [odběru aktualizací služeb Lab Services,](https://azure.microsoft.com/updates/?product=lab-services) abyste byli informováni o nových funkcích ve službách Lab Services.

## <a name="general"></a>Obecné
### <a name="what-if-my-question-isnt-answered-here"></a>Co když moje otázka není zodpovězena tady?
Pokud zde vaše otázka není uvedena, dejte nám vědět, abychom vám pomohli najít odpověď.

- Zveřejněte dotaz na konci tohoto faq. 
- Chcete-li oslovit širší okruh uživatelů, zveřejněte dotaz na [fórum Azure Lab Services – Stack Overflow](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- V případě žádostí o funkce odešlete své požadavky a nápady do [služby Azure Lab Services – User Voice](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

