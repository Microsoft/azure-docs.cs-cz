---
title: Aktualizace dat analýzy hrozeb
description: Datový balíček Analýza hrozeb je k dispozici pro každý nový Defender pro verzi IoT nebo v případě potřeby mezi verzemi.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: bb38d0a2486bda336d6881ec6f4c5d680906c973
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750459"
---
# <a name="threat-intelligence-research-and-packages"></a>Analýzy a balíčky pro analýzu hrozeb #
## <a name="overview"></a>Přehled ##

Týmy zabezpečení v Microsoftu provádějí proprietární vlastní funkce a analýzy ohrožení zabezpečení ICS. Mezi tyto týmy patří MSTIC (Microsoft Threat Intelligence Center), DART (Microsoft Detection and Response Team), DCU (digitální zločiny) a oddíl 52 (experti na domény IoT/pro/ICS, které sledují nulové dny, zpětné strojírenství a nežádoucí osoby) pro sdílení sdíleného softwaru.

Týmy poskytují detekci, analýzu a reakci na zabezpečení Microsoftu:

- Cloudová infrastruktura a služby.
- Tradiční produkty a zařízení.
- Interní podnikové prostředky.

Týmy zabezpečení získají výhody pro:

- Ochrana před známými a relevantními hrozbami.
- Přehledy, které vám pomůžou při třídění a stanovení priorit.
- Porozumění úplnému kontextu hrozeb před tím, než se to týká.
- Relevantnější, přesné a napadnutelná data.

Tato funkce poskytuje kontextové informace pro rozšíření Microsoft Platform Analytics a podporuje spravované služby společnosti za účelem reakce na incidenty a vyšetřování porušení zabezpečení. Balíčky analýzy hrozeb obsahují signatury (včetně signatur malwaru), CVEs a další obsah zabezpečení.

## <a name="when-are-packages-delivered"></a>Kdy jsou doručeny balíčky ##

Balíčky analýzy hrozeb jsou k dispozici přibližně jednou měsíčně nebo v případě potřeby častěji. Oznámení o nových balíčcích jsou k dispozici zde: https://techcommunity.microsoft.com/t5/azure-defender-for-iot/bd-p/AzureDefenderIoT . 

## <a name="update-threat-intelligence-packages-to-your-sensors"></a>Aktualizace balíčků pro analýzu hrozeb na senzory ##

K dispozici jsou tři možnosti aktualizace balíčků pro analýzu hrozeb na senzory:

- Automatické vkládání balíčků na senzory, které jsou dodávány programem Defender pro IoT.
- Ruční vložení balíčku analýzy hrozeb do senzorů podle potřeby
- Stáhněte si balíček a nahrajte ho na senzor nebo víc senzorů.

Uživatelé s programem Defender pro čtenáře zabezpečení IoT můžou automaticky a ručně vkládat balíčky do senzorů.

### <a name="automatically-push-threat-intelligence-updates-to-sensors"></a>Automatické vkládání aktualizací pro analýzy hrozeb do senzorů ###

Balíčky analýzy hrozeb se dají automaticky aktualizovat na senzory *připojené ke cloudu* , protože jsou vydané v programu Defender pro IoT. Ujistěte se, že se automatická aktualizace balíčku připojuje ke senzoru připojenému ke cloudu s povolenou možností **aktualizace funkce Analýza hrozeb** . Další informace najdete v tématu připojení [snímače](getting-started.md#onboard-a-sensor).

### <a name="manually-push-threat-intelligence-updates-to-sensors"></a>Ruční vložení aktualizací pro analýzy hrozeb do senzorů ###

Senzory *připojené ke cloudu* se dají automaticky aktualizovat pomocí balíčků pro analýzu hrozeb. Pokud byste ale chtěli získat konzervativní přístup, můžete balíčky z Azure Defenderu pro IoT Portal nabízet jenom v případě, že je to nutné.
Díky tomu máte možnost řídit, kdy se balíček nainstaluje, aniž byste ho museli stahovat a načítat na senzory.

**Ruční vložení balíčků:**

1. Přejít na stránku Azure Defender for IoT **Web and snímačs** .
1. Vyberte tři tečky (...) pro senzor a pak vyberte **aktualizace Intelligence pro nabízení hrozeb**. V poli **stav aktualizace analýzy hrozeb** se zobrazí průběh aktualizace.

#### <a name="change-the-threat-intelligence-update-mode"></a>Změna režimu aktualizace Intelligence Threat ####

Po počáteční registraci můžete změnit režim aktualizace Intelligence Threat Threat.

**Postup změny režimu aktualizace:**

1. Vyberte tři tečky (...) pro senzor a pak vyberte **Upravit**.
1. Povolí nebo zakáže přepínač **automatických aktualizací pro analýzu hrozeb** .

### <a name="download-packages-and-upload-to-sensors"></a>Stažení balíčků a nahrávání na senzory ###

Balíčky je možné stáhnout z Defenderu pro IoT Portal a ručně nahrávat do jednotlivých senzorů. Pokud místní Konzola pro správu spravuje vaše senzory, můžete si stáhnout balíčky pro analýzy hrozeb do konzoly pro správu a současně je vložit do více senzorů.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Stáhněte si aktualizace prostřednictvím Azure Defenderu pro IoT Portal.":::

Tato možnost je k dispozici pro *cloudové* i *místně spravované* senzory.

**Postup nahrání do jednoho snímače:**

1. Přejít na stránku **aktualizace** pro Azure Defender pro IoT.

2. Stáhněte a uložte balíček pro **analýzu hrozeb** .

3. Přihlaste se ke konzole senzorů.

4. V postranní nabídce vyberte **nastavení systému**.

5. Vyberte **data analýzy hrozeb** a pak vyberte **aktualizovat**.

6. Nahrajte nový balíček.

**Jak nahrávat přes víc senzorů současně:**

1. Přejít na stránku **aktualizace** pro Azure Defender pro IoT.

2. Stáhněte a uložte balíček pro **analýzu hrozeb** .

3. Přihlaste se ke konzole pro správu.

4. V postranní nabídce vyberte **nastavení systému**.

5. V části **Konfigurace modulu senzorů** vyberte senzory, které by měly dostávat aktualizované balíčky.  

6. V části **Vybrat data analýzy hrozeb** vyberte znaménko plus ( **+** ).

7. Nahrajte balíček.

## <a name="review-package-update-status-on-the-sensor"></a>Kontrola stavu aktualizace balíčku na senzoru ##

Informace o stavu aktualizace balíčku a verzi se zobrazí v části **nastavení systému** senzorů, **Analýza hrozeb** .  

## <a name="review-package-information-for-cloud-connected-sensors"></a>Kontrola informací o balíčku pro senzory připojené k cloudu ##

Přečtěte si následující informace o balíčcích hrozeb pro cloudy, které jste připojili ke senzorům cloudu:

- Nainstalovaná verze balíčku
- Režim aktualizace Intelligence Threat
- Stav aktualizace Intelligence Threat

Kontrola informací o analýze hrozeb:

1. Přejít na stránku Azure Defender for IoT **Web and snímačs** .
1. Zkontrolujte **verzi analýzy hrozeb** nainstalovanou na jednotlivých senzorech. Pojmenovávání verzí je založené na dni, kdy byl balíček sestavený v programu Defender pro IoT.
1. Projděte si **režim analýzy hrozeb** . *Automaticky* znamená, že nově dostupné balíčky budou automaticky nainstalovány na senzory, které jsou vydány programem Defender pro IoT. *Ruční* označuje, že podle potřeby můžete nabízet nově dostupné balíčky přímo na senzory.
1. Zkontrolujte **stav aktualizace analýzy hrozeb**. Můžou se zobrazit následující stavy:

- Neúspěšný
- In Progress
- Dostupná aktualizace
- Ok

Pokud se aktualizace analýzy hrozeb připojené k cloudu nezdaří, zkontrolujte informace o připojení ve sloupci **stav senzoru** a **naposledy připojené sloupce UTC** na stránce **lokality a senzory** . 

## <a name="see-also"></a>Viz také

[Připojení snímače](getting-started.md#onboard-a-sensor)

[Správa senzorů z konzoly pro správu](how-to-manage-sensors-from-the-on-premises-management-console.md)