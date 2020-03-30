---
title: Image Red Hat Enterprise Linux v Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o inacích Red Hat Enterprise Linux v Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: f06c4304be67fbc2f3116375dae33b10228723a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239878"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Přehled obrázků Red Hat Enterprise Linux

Tento článek popisuje dostupné image Red Hat Enterprise Linux (RHEL) na Azure Marketplace a zásady týkající se jejich pojmenování a uchovávání.

Informace o zásadách podpory Red Hat pro všechny verze rhel, naleznete v tématu [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata). Podrobnosti o cenách najdete v [tématu Azure pricing calculator](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> Image RHEL, které jsou momentálně dostupné na Azure Marketplace, podporují buď modely licencování s průběžnými platbami s vaším vlastním předplatným (BYOS), nebo s průběžnými licenčními modely. [Výhoda hybridního využití Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) a dynamické přepínání mezi BYOS a licencováním s průběžnými platbami není podporováno. Chcete-li přepnout režim licencování, je nutné znovu nasadit virtuální ho virtuálního zařízení z odpovídající bitové kopie.

>[!NOTE]
> Pro všechny problémy související s ibi RHEL na Azure Marketplace, soubor lístek podpory s Microsoftem.

## <a name="view-images-available-in-azure"></a>Zobrazení ibi dostupných v Azure

Když hledáte "Red Hat" na Azure Marketplace nebo když vytvoříte prostředek v unovém rozhraní portálu Azure, uvidíte jenom podmnožinu všech dostupných ibií RHEL. Celou sadu dostupných ibi virtuálních počítačů můžete získat vždy pomocí rozhraní API Azure, PowerShellu a rozhraní API.

Chcete-li zobrazit úplnou sadu dostupných ibi Red Hat v Azure, spusťte následující příkaz:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Konvence

Image virtuálních her v Azure jsou uspořádané podle vydavatele, nabídky, skladové položky a verze. Kombinace Publisher:Offer:SKU:Version je verno image a jednoznačně identifikuje obrázek, který má být použit.

Například `RedHat:RHEL:8-LVM:8.1.20200318` odkazuje na OBRÁZEK rhel 8.1 LVM-partitioned postavený na 18 března 2020.

Ukázka vytvoření virtuálního virtuálního jevu RHEL 8.1, která se zobrazí zde.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>"Nejnovější" zástupné spoje

Rozhraní API Azure REST umožňuje použití zástupného název "nejnovější" pro verzi namísto konkrétní verze. Použití "nejnovější" ustanovení nejnovější dostupné image pro daného vydavatele, nabídky a skladové položky.

Například `RedHat:RHEL:8-LVM:latest` odkazuje na nejnovější RHEL 8 řady LVM rozdělenimage k dispozici.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> Obecně platí porovnání verzí k určení nejnovější postupujte podle pravidel [CompareTo metody](https://msdn.microsoft.com/library/a5ts8tb6.aspx).
Toto porovnání verzí bitové kopie se provádí porovnáním hodnot jako objektu [Version,](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) nikoli jako řetězce.

## <a name="rhel-6-image-types"></a>RHEL 6 typů obrázků

U obrazů RHEL 6.x jsou typy obrázků zobrazeny v následující tabulce.

|Vydavatel | Nabídka | Hodnota skladové položky | Version | Podrobnosti
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Dílčí verze (například 6.9) | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 6.9.2018010506) | Všechny standardní obrázky RHEL 6.x se řídí touto úmluvou.
|RedHat | rhel-byos | rhel-raw69 | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 6.9.20181023) | Tento obrázek je rhel 6.9 BYOS obraz.
|RedHat | RHEL | RHEL-SAP-APPS | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 6.8.2017053118) | Tento obrázek je rhel 6.8 pro SAP aplikace image. Má právo na přístup k úložištím SAP Applications a základním úložištím RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 6.7.2017053121) | Tento obrázek je RHEL 6.7 pro SAP HANA image. Má právo na přístup k úložištím SAP HANA a základním úložištím RHEL.

## <a name="rhel-7-image-types"></a>Typy obrázků RHEL 7

U obrázků RHEL 7.x existuje několik různých typů obrázků. V následující tabulce jsou uvedeny různé sady obrázků, které nabízíme. Pokud chcete zobrazit úplný seznam, použijte `az vm image list --publisher redhat --all`příkaz Azure CLI .

>[!NOTE]
> Pokud není uvedeno jinak, všechny obrázky jsou rozděleny do oddílů LVM a připojují se k běžným repozitářům RHEL. To znamená, že úložiště nejsou rozšířená podpora aktualizací (EUS) a nejsou aktualizační služby pro SAP (E4S). Do budoucna se přesouváme k publikování pouze lvm-partitioned obrázky, ale jsou otevřeny pro zpětnou vazbu na toto rozhodnutí. Další informace o rozšířené podpoře aktualizací a aktualizačních službách pro SAP naleznete [v tématu Red Hat Enterprise Linux life cycle](https://access.redhat.com/support/policy/updates/errata).

|Vydavatel | Nabídka | Hodnota skladové položky | Version | Podrobnosti
|----------|-------|------------|---------|--------
|RedHat | RHEL | Dílčí verze (například 7.6) | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 7.6.2019102813) | Obrázky publikované před dubnem 2019 jsou připojeny ke standardním repozitářům RHEL. Obrázky zveřejněné po dubnu 2019 jsou připojeny k repozitářům EUS společnosti Red Hat, aby bylo možné uzamknout verzi konkrétní dílčí verze. Zákazníci, kteří chtějí pravidelné repozitáře, by měli používat obrázky, které obsahují 7-LVM nebo 7-RAW v hodnotě Skladové položky (podrobnosti následují). Rhel 7.7 a novější obrázky jsou LVM rozděleny. Všechny ostatní obrázky v této kategorii jsou nezpracované oddíly.
|RedHat | RHEL | 7-SUROVÝ | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 7.6.2019102813) | Tyto obrázky jsou nezpracované rozdělené (například nebyly přidány žádné logické svazky).
|RedHat | RHEL | 7-RAW-CI | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 7.6.2019072418) | Tyto image jsou nezpracované rozdělené (například nebyly přidány žádné logické svazky) a používají cloud-init pro zřizování.
|RedHat | RHEL | 7-LVM | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 7.6.2019062414) | Tyto obrázky jsou LVM rozděleny.
|RedHat | rhel-byos | rhel-{lvm,raw} | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 7.7.20190819) | Tyto obrázky jsou obrazy RHEL 7 BYOS. Nejsou připojeny k žádným úložištím a nebudou účtovat prémiový poplatek RHEL. Máte-li zájem o obrázky RHEL BYOS, [požádejte o přístup](https://aka.ms/rhel-byos). Hodnoty skladové položky končí dílčí verzí a označují, zda je bitová kopie nezpracovaná nebo lVM rozdělená na oddíly. Například hodnota skladové položky rhel-lvm77 označuje bitovou kopii RHEL 7.7 s oddíly LVM.
|RedHat | RHEL | RHEL-SAP | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 7.6.2019071300) | Tyto obrázky jsou RHEL pro obrázky SAP. Mají právo na přístup k úložištím SAP HANA a Aplikací, stejně jako k úložištím RHEL E4S. Fakturace zahrnuje prémii RHEL a prémii SAP nad základní výpočetní poplatek.
|RedHat | RHEL | RHEL-SAP-HA | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 7.6.2019062320) | Tyto obrázky jsou RHEL pro SAP s vysokou dostupností a aktualizace služby obrázky. Mají právo na přístup k úložištím SAP HANA a Aplikacím a úložištím s vysokou dostupností, stejně jako k úložištím RHEL E4S. Fakturace zahrnuje prémii RHEL, prémii SAP a prémii s vysokou dostupností nad základní výpočetní poplatek.
|RedHat | RHEL | RÝM-HA | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 7.6.2019062019) | Tyto obrázky jsou obrázky RHEL, které mají také nárok na přístup k doplňku Vysoká dostupnost. Účtují si mírně navíc na vrcholu RHEL a základní výpočetní poplatek kvůli doplňkové muka s vysokou dostupností.
|RedHat | RHEL | RHEL-SAP-APPS | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 7.3.2017053118) | Tyto obrázky jsou zastaralé, protože aplikace SAP a úložiště SAP HANA byly sloučeny do úložišť SAP. Tyto obrázky jsou RHEL pro sap aplikace obrázky. Mají právo na přístup k úložištím SAP Applications a základním úložištím RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 7.3.2018051421) | Tyto obrázky jsou zastaralé, protože aplikace SAP a úložiště SAP HANA byly sloučeny do úložišť SAP. Tyto obrázky jsou RHEL pro obrázky SAP HANA. Mají právo na přístup k úložištím SAP HANA a základním úložištím RHEL.

## <a name="rhel-8-image-types"></a>Rhel 8 typů obrázků

Podrobnosti o typech obrázků RHEL 8 jsou uvedeny níže.

|Vydavatel | Nabídka | Hodnota skladové položky | Version | Podrobnosti
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 8.0.20191023) | Tyto obrázky jsou RHEL 8 LVM-dělené obrazy připojené ke standardním Red Hat repozitářů.
|RedHat | RHEL | 8 gen2 | Zřetězené hodnoty dílčí verze RHEL a data publikována (například 8.0.20191024) | Tyto obrázky jsou Hyper-V Generace 2 RHEL 8 LVM-dělené obrazy připojené ke standardním Red Hat repozitáře. Další informace o virtuálních počítačích generace 2 v Azure najdete v [tématu Podpora virtuálních počítačích generace 2 v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2).

## <a name="rhel-longer-support-add-ons"></a>Doplňky s delší podporou RHEL

### <a name="extended-update-support"></a>Rozšířená podpora aktualizací

Od dubna 2019 jsou k dispozici obrázky RHEL, které jsou ve výchozím nastavení připojeny k úložištím EUS. Více informací o RHEL EUS je k dispozici v [dokumentaci red hatu](https://access.redhat.com/articles/rhel-eus).

Přechod na repozitáře EUS je možný a je podporován. Pokyny, jak přepnout virtuální počítač na EUS a další informace o termínech ukončení životnosti podpory EUS, najdete v [tématu RHEL EUS a virtuálnípočítače RHEL .](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)

>[!NOTE]
> EUS není podporována na RHEL Extras. To znamená, že pokud nainstalujete balíček, který je obvykle k dispozici z kanálu RHEL Extras, nebudete tak moci učinit v EUS. Další informace o životním cyklu produktu Red Hat Extras najdete v [tématu Red Hat Enterprise Linux Extras životní cyklus](https://access.redhat.com/support/policy/updates/extras/).

#### <a name="differentiate-between-regular-and-eus-images"></a>Rozlišovat mezi pravidelnými obrázky a obrázky EUS

Zákazníci, kteří chtějí používat obrázky připojené k úložištím EUS, by měli používat bitovou kopii RHEL, která obsahuje číslo dílčí verze RHEL ve skladové jednotce.

Například se mohou zobrazit následující dva obrázky RHEL 7.4 k dispozici.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

V tomto `RedHat:RHEL:7.6:7.6.2019102813` případě je ve výchozím nastavení připojen k úložištím EUS. Hodnota skladové položky je 7,4. A `RedHat:RHEL:7-LVM:7.6.2019062414` ve výchozím nastavení je připojen k úložištím mimo EUS. Hodnota skladové položky je 7-LVM.

Chcete-li používat běžné úložiště (mimo EUS), použijte obrázek, který ve skladové jednotce neobsahuje číslo dílčí verze.

#### <a name="rhel-images-with-eus"></a>Obrázky RHEL s EUS

Informace v následující tabulce platí pro obrázky RHEL, které jsou připojeny k úložištím EUS.

>[!NOTE]
> V době psaní tohoto článku mají podporu EUS pouze verze RHEL 7.4 a novější verze. EUS již není podporována pro rhel <=7.3.
>
> Další informace o dostupnosti RHEL EUS naleznete [v tématu Red Hat Enterprise Linux life cycle](https://access.redhat.com/support/policy/updates/errata).

Podverze |Příklad obrázku EUS              |Status EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat:RHEL:7.4:7.4.2019041718 | Obrázky zveřejněné v dubnu 2019 a později jsou ve výchozím nastavení eus.|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Obrázky zveřejněné v červnu 2019 a později jsou ve výchozím nastavení eus. |
RHEL 7,6      |RedHat:RHEL:7.6:7.6.2019052206 | Obrázky zveřejněné v květnu 2019 a později jsou ve výchozím nastavení eus. |
RHEL 8,0      |Není dostupné.                            | Od Red Hatu není k dispozici žádný EUS.                               |

### <a name="update-services-for-sap"></a>Aktualizovat služby pro SAP

Nejnovější image RHEL pro SAP budou připojeny k předplatným aktualizačních služeb pro řešení SAP (E4S). Další informace o e4S naleznete v [dokumentaci](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)k Red Hat .

#### <a name="rhel-images-with-e4s"></a>Obrázky RHEL s E4S

Obrázky z následujících nabídek vytvořených po prosinci 2019 jsou připojeny k úložištím E4S:

* RHEL-SAP (RHEL pro SAP)
* RHEL-SAP-HA (RHEL pro SAP s vysokou dostupností a aktualizačními službami)

## <a name="other-available-offers-and-skus"></a>Další dostupné nabídky a SKU

Úplný seznam dostupných nabídek a sku mohou obsahovat další obrázky nad rámec toho, co je uvedeno v předchozí tabulce. Příklad: `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Tyto nabídky mohou být použity k poskytování podpory pro konkrétní řešení marketplace. Nebo mohou být publikovány pro účely náhledů a testování. Mohou být kdykoli bez varování změněny nebo odstraněny. Nepoužívejte je, pokud jejich přítomnost není veřejně zdokumentována společností Microsoft nebo Red Hat.

## <a name="publishing-policy"></a>Zásady publikování

Microsoft a Red Hat aktualizace bitových kopií jako nové dílčí verze jsou vydávány, podle potřeby řešit konkrétní běžné chyby zabezpečení a expozice (CVEs) nebo pro příležitostné změny konfigurace nebo aktualizace. Snažíme se poskytovat aktualizované obrázky co nejdříve do tří pracovních dnů po vydání nebo dostupnosti opravy CVE.

Aktualizujeme pouze aktuální dílčí verzi v dané rodině obrázků. S vydáním novější dílčí verze přestaneme aktualizovat starší dílčí verzi. Například s vydáním RHEL 7.6, RHEL 7.5 obrázky již nejsou aktualizovány.

>[!NOTE]
> Aktivní virtuální počítače Azure zřízené z bitových kopií RHEL s průběžnými platbami jsou připojené k Azure RHUI a můžou přijímat aktualizace a opravy, jakmile je vydá Red Hat a replikuje do Azure RHUI. Načasování je obvykle méně než 24 hodin po oficiálním vydání Red Hat. Tyto virtuální virtuální měšce nevyžadují novou publikovanou bitovou kopii pro získání aktualizací. Zákazníci mají plnou kontrolu nad tím, kdy chcete aktualizaci zahájit.

## <a name="image-retention-policy"></a>Zásady uchovávání obrázků

Aktuální zásadou je zachovat všechny dříve publikované obrázky. Vyhrazujeme si právo odstranit obrázky, o kterých je známo, že způsobují problémy jakéhokoli druhu. Například obrázky s nesprávnou konfigurací z důvodu následné aktualizace platformy nebo součásti mohou být odebrány. Image, které mohou být odebrány, se řídí aktuálními zásadami Azure Marketplace a poskytují oznámení až 30 dní před odebráním bitové kopie.

## <a name="next-steps"></a>Další kroky

* Úplný seznam ibi v YRV u Něj najdete v tématu [Image Red Hat Enterprise Linux (RHEL) dostupné v Azure](./redhat-imagelist.md).
* Další informace o infrastruktuře aktualizací Azure Red Hat najdete v [tématu Infrastruktura aktualizací Red Hat pro virtuální počítače RHEL na vyžádání v Azure](https://aka.ms/rhui-update).
* Další informace o nabídce RHEL BYOS najdete v tématu [Red Hat Enterprise Linux , který vám přinese vlastní předplatné, zlaté obrázky v Azure](./byos.md).
* Informace o zásadách podpory Red Hat pro všechny verze rhel, naleznete v tématu [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata).
