---
title: Přehled Red Hat Enterprise Linux imagí v Azure
description: Přečtěte si o Red Hat Enterprise Linuxch imagí v Microsoft Azure.
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 02/10/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 42e0788a25efa5124f24a77b48469d6ed8265dfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694678"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Přehled Red Hat Enterprise Linuxch imagí

Tento článek popisuje dostupné Red Hat Enterprise Linux (RHEL) imagí v Azure Marketplace a zásadách pro jejich pojmenování a uchovávání.

Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete v tématu [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata). Podrobnosti o cenách najdete v tématu [Cenová Kalkulačka Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> RHEL image aktuálně dostupné v Azure Marketplace podporují i modely licencování BYOS (Přineste si vlastní předplatné) nebo průběžné platby. Dynamické přepínání mezi BYOS a licencováním s průběžnými platbami se dá dělat prostřednictvím [zvýhodněné hybridní využití Azure](../../linux/azure-hybrid-benefit-linux.md).

>[!NOTE]
> Pro všechny problémy související s RHEL obrázky v Azure Marketplace si zaregistrujte lístek podpory s Microsoftem.

## <a name="view-images-available-in-azure"></a>Zobrazení imagí dostupných v Azure

Při hledání "Red Hat" v Azure Marketplace nebo při vytváření prostředku v uživatelském rozhraní Azure Portal se zobrazí pouze podmnožina všech dostupných imagí RHEL. Úplnou sadu dostupných imagí virtuálních počítačů můžete kdykoli získat pomocí rozhraní příkazového řádku Azure CLI, PowerShellu a rozhraní API.

Pokud chcete zobrazit úplnou sadu dostupných imagí Red Hat v Azure, spusťte následující příkaz:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Konvence pojmenování

Image virtuálních počítačů v Azure jsou seřazené podle vydavatele, nabídky, SKU a verze. Kombinace vydavatele: nabídka: SKU: verze je identifikátor URN image a jednoznačně identifikuje obrázek, který se má použít.

Například `RedHat:RHEL:8-LVM:8.1.20200318` odkazuje na bitovou kopii RHEL 8,1 LVM-partition vytvořenou v 18. březnu 2020.

Ukázka, jak vytvořit virtuální počítač s RHEL 8,1, se zobrazí zde.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>Moniker "poslední"

Azure REST API umožňuje použít moniker "nejnovější" pro verzi místo konkrétní verze. Při použití možnosti "poslední" se zřídí nejnovější dostupná image pro daného vydavatele, nabídku a SKU.

Například `RedHat:RHEL:8-LVM:latest` odkazuje na nejnovější dostupnou bitovou kopii RHEL 8 Family LVM-Partitioned.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> Obecně platí, že porovnání verzí za účelem určení nejnovější následuje pravidla [metody CompareTo](/dotnet/api/system.version.compareto#system_version_compareto_system_version_).
Porovnání verzí tohoto obrázku je provedeno porovnáním hodnot jako objektu [verze](/dotnet/api/system.version.-ctor) , nikoli jako řetězce.

## <a name="rhel-6-image-types"></a>Typy imagí RHEL 6

>[!NOTE]
> Od prosince 30 2020 RHEL 6,10 zadal konec životnosti (konce řádku). Pokud chcete pokračovat v podpoře, povolte ELS jako součást fáze podpory rozšířené životního cyklu. Další informace najdete v [dokumentaci k Red Hat ELS](./redhat-extended-lifecycle-support.md).

Pro image RHEL 6. x jsou typy obrázků uvedené v následující tabulce.

|Publisher | Nabídka | Hodnota SKU | Verze | Podrobnosti
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Dílčí verze (například 6,9) | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 6.9.2018010506) | Všechny standardní image RHEL 6. x se řídí touto konvencí.
|RedHat | RHEL – BYOS | RHEL – raw69 | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 6.9.20181023) | Tato Image je image RHEL 6,9 BYOS.
|RedHat | RHEL | RHEL-SAP-APLIKACE | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 6.8.2017053118) | Tato Image je image RHEL 6,8 pro aplikace SAP. Má oprávnění k přístupu k úložištím aplikací SAP a základním úložištím RHEL.
|RedHat | RHEL | RHEL – SAP – HANA | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 6.7.2017053121) | Tento obrázek je RHEL 6,7 pro Image SAP HANA. Má přístup k SAP HANA úložišť a základních úložišť RHEL.

## <a name="rhel-7-image-types"></a>RHEL 7 – typy obrázků

Pro image RHEL 7. x existuje několik různých typů obrázků. V následující tabulce jsou uvedeny různé sady imagí, které nabízíme. Úplný seznam zobrazíte pomocí příkazu rozhraní příkazového řádku Azure CLI `az vm image list --publisher redhat --all` .

>[!NOTE]
> Pokud není uvedeno jinak, všechny image jsou LVM rozdělené do oddílů a připojují se k běžným úložištím RHEL. To znamená, že v úložištích nejsou Rozšířená podpora aktualizací (EUS) a ne služby aktualizace pro SAP (E4S). Až budete přesměrováni na publikování pouze imagí LVM, ale otevře se pro názory na toto rozhodnutí. Další informace o podpoře rozšířených aktualizací a aktualizacích služeb pro SAP najdete v tématu [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata).

|Publisher | Nabídka | Hodnota SKU | Verze | Podrobnosti
|----------|-------|------------|---------|--------
|RedHat | RHEL | Dílčí verze (například 7,6) | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 7.6.2019102813) | Image publikované před dubna 2019 jsou připojené ke standardním úložištím RHEL. Image publikované po dubna 2019 jsou připojené k úložištím EUS Red Hat, aby bylo možné uzamknout určitou verzi konkrétní dílčí verze. Zákazníci, kteří chtějí pravidelná úložiště, by měli použít image, které v hodnotě SKU obsahují 7 – LVM nebo 7-RAW (podrobnosti následují). RHEL 7,7 a novější obrázky jsou LVM rozdělené na oddíly. Všechny ostatní obrázky v této kategorii jsou nezpracované dělené.
|RedHat | RHEL | 7 – RAW | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 7.6.2019102813) | Tyto image nejsou rozdělené do oddílů (například nebyly přidány žádné logické svazky).
|RedHat | RHEL | 7-RAW-CI | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 7.6.2019072418) | Tyto image nejsou rozdělené do oddílů (například nepřidaly se žádné logické svazky) a pro zřizování používají Cloud-init.
|RedHat | RHEL | 7 – LVM | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 7.6.2019062414) | Tyto image jsou LVM rozdělené do oddílů.
|RedHat | RHEL – BYOS | RHEL-{LVM, RAW} | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 7.7.20190819) | Tyto image jsou image BYOS v RHEL 7. Nejsou připojené k žádným úložištím a poplatky za RHEL Premium se neúčtují. Pokud máte zájem o image RHEL BYOS, [vyžádejte si přístup](https://aka.ms/rhel-byos). Hodnoty SKU končí podverzí a označují, zda je obrázek RAW nebo LVM rozdělený na oddíly. Například hodnota SKU RHEL-lvm77 označuje obrázek RHEL 7,7 s LVM oddíly.
|RedHat | RHEL | RHEL – SAP | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 7.6.2019071300) | Tyto image jsou RHEL pro Image SAP. Mají nárok na přístup k úložištím SAP HANA a aplikacím a také k úložištím E4S v RHEL. Účtování zahrnuje RHEL Premium a SAP Premium nad základním výpočetním poplatkem.
|RedHat | RHEL | RHEL-SAP-HA | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 7.6.2019062320) | Tyto image jsou RHEL pro SAP s imagí pro vysokou dostupnost a službu Update Services. Mají oprávnění k přístupu k úložištím SAP HANA a aplikací a k úložištím s vysokou dostupností a také k úložištím RHEL E4S. Účtování zahrnuje základní výpočetní poplatek na úrovni Premium RHEL Premium, SAP Premium a vysoká dostupnost.
|RedHat | RHEL | RHEL-HA | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 7.6.2019062019) | Tyto image jsou RHEL obrázky, které mají také oprávnění k přístupu k doplňku vysoké dostupnosti. Účtují se trochu navíc nad RHEL a základním poplatkem za výpočetní výkon v důsledku doplňku vysoké dostupnosti Premium.
|RedHat | RHEL | RHEL-SAP-APLIKACE | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 7.3.2017053118) | Tyto image jsou zastaralé, protože aplikace SAP a SAP HANA úložiště byly sloučeny do úložišť SAP. Tyto image jsou RHEL pro Image aplikací SAP. Mají oprávnění získat přístup k úložištím aplikací SAP a základním úložištím RHEL.
|RedHat | RHEL | RHEL – SAP – HANA | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 7.3.2018051421) | Tyto image jsou zastaralé, protože aplikace SAP a SAP HANA úložiště byly sloučeny do úložišť SAP. Tyto obrázky jsou RHEL for SAP HANA obrázky. Mají oprávnění pro přístup k SAP HANA úložišť a základních úložišť RHEL.

## <a name="rhel-8-image-types"></a>Typy imagí RHEL 8

>[!NOTE]
> Red Hat doporučuje pomocí Grubby nakonfigurovat parametry příkazového řádku jádra v RHEL 8 +. Další podrobnosti jsou k dispozici [zde](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel).

Podrobnosti o typech imagí RHEL 8 jsou uvedené níže.

|Publisher | Nabídka | Hodnota SKU | Verze | Podrobnosti
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 8.0.20191023) | Tyto image jsou image s RHEL 8 LVM oddíly připojené ke standardním úložištím Red Hat.
|RedHat | RHEL | 8 – Gen2 | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 8.0.20191024) | Tyto image jsou image Hyper-V generace 2 RHEL 8 LVM-dělené bitové kopie připojené ke standardním úložištím Red Hat. Další informace o virtuálních počítačích 2. generace v Azure najdete v tématu [Podpora virtuálních počítačů 2. generace v Azure](../../generation-2.md).
|RedHat | RHEL | RHEL-SAP-APLIKACE | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 8.1.2021012201) | Tyto image jsou RHEL pro Image aplikací SAP. Mají oprávnění získat přístup k úložištím aplikací SAP a základním úložištím RHEL.
|RedHat | RHEL | RHEL-SAP-HA | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (například 8.1.2021010602) | Tyto image jsou RHEL pro SAP s imagí pro vysokou dostupnost a službu Update Services. Mají oprávnění k přístupu k úložištím řešení a aplikací SAP a k úložištím s vysokou dostupností a také k úložištím RHEL E4S. Účtování zahrnuje základní výpočetní poplatek na úrovni Premium RHEL Premium, SAP Premium a vysoká dostupnost.

## <a name="rhel-extended-support-add-ons"></a>Doplňky RHEL Extended Support Doplňky

### <a name="extended-life-cycle-support"></a>Rozšířená podpora životního cyklu

Doplněk prodloužená podpora životního cyklu (ELS) je volitelný odběr, který umožňuje kritické a důležité opravy zabezpečení pro vydané verze, které dosáhly konce životnosti (konce řádku). Další informace o RHEL EUS najdete v [dokumentaci k Red Hat](https://access.redhat.com/support/policy/updates/errata#Extended_Life_Cycle_Support).

ELS je aktuálně dostupná jenom pro RHEL 6,10. V případě imagí s průběžnými platbami můžete ELS povolit podle kroků v [dokumentaci k Red Hat ELS](./redhat-extended-lifecycle-support.md).

Pokud používáte starší verzi nástroje, je potřeba upgradovat na RHEL 6,10, abyste mohli povolit ELS.

### <a name="extended-update-support"></a>Podpora rozšířené aktualizace

Od dubna 2019 jsou k dispozici image RHEL, které jsou ve výchozím nastavení připojené k úložištím EUS. Další informace o RHEL EUS najdete v [dokumentaci k Red Hat](https://access.redhat.com/articles/rhel-eus).

Přepínání na úložiště EUS je možné a podporuje se. Pokyny, jak přepnout virtuální počítač na EUS a další informace o datech ukončení životnosti EUS, najdete v článku [RHEL EUS a virtuální počítače s zámky verze RHEL](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS se v RHEL Extras nepodporuje. To znamená, že pokud nainstalujete balíček, který je obvykle dostupný z kanálu RHEL Extras, nebudete ho moct v EUS použít. Další informace o životním cyklu produktu Red Hat extra získáte v tématu [Red Hat Enterprise Linux životní cyklus dalších](https://access.redhat.com/support/policy/updates/extras/).

#### <a name="differentiate-between-regular-and-eus-images"></a>Rozlišení mezi pravidelnými a EUS obrázky

Zákazníci, kteří chtějí používat image připojené k úložištím EUS, by měli použít image RHEL, která obsahuje číslo podverze RHEL v SKU.

Můžete například zobrazit následující dvě dostupné image RHEL 7,4.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

V tomto případě `RedHat:RHEL:7.6:7.6.2019102813` je ve výchozím nastavení k úložištím EUS připojeno. Hodnota SKU je 7,4. A `RedHat:RHEL:7-LVM:7.6.2019062414` je ve výchozím nastavení připojené k úložištím bez EUS. Hodnota SKU je 7 – LVM.

Chcete-li používat pravidelná úložiště (bez EUS), použijte obrázek, který neobsahuje číslo podverze v SKU.

#### <a name="rhel-images-with-eus"></a>Image RHEL s EUS

Informace v následující tabulce se vztahují na RHEL image připojené k úložištím EUS.

>[!NOTE]
> V době psaní mají EUS podporu jenom RHEL 7,4 a novější podverze. EUS se už nepodporuje pro RHEL <= 7,3.
>
> Další informace o dostupnosti RHEL EUS najdete v tématu [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata).

Podverze |Příklad obrázku EUS              |Stav EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat: RHEL: 7.4:7.4.2019041718 | Obrázky publikované v dubnu 2019 a novějších jsou ve výchozím nastavení EUS.|
RHEL 7.5      |RedHat: RHEL: 7.5:7.5.2019060305 | Obrázky publikované od června 2019 a novějších jsou EUS ve výchozím nastavení. |
RHEL 7,6      |RedHat: RHEL: 7.6:7.6.2019052206 | Publikované obrázky mohou být 2019 a novější EUS ve výchozím nastavení. |
RHEL 8,0      |–                            | V Red Hat není k dispozici žádný EUS.                               |

### <a name="update-services-for-sap"></a>Aktualizace služeb pro SAP

Nejnovější image RHEL for SAP budou připojeny ke službám Update Services for SAP Solutions Subscriptions (E4S). Další informace o E4S najdete v [dokumentaci](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)k Red Hat.

#### <a name="rhel-images-with-e4s"></a>Image RHEL s E4S

Obrázky z následujících nabídek vytvořené po prosinci 2019 jsou připojené k úložištím E4S:

* RHEL-SAP (RHEL pro SAP)
* RHEL-SAP-HA (RHEL pro SAP s vysokou dostupností a aktualizací Update Services)

## <a name="other-available-offers-and-skus"></a>Další dostupné nabídky a SKU

Úplný seznam dostupných nabídek a SKU může obsahovat další obrázky nad rámec toho, co je uvedeno v předchozí tabulce. Příklad: `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Tyto nabídky mohou sloužit k poskytování podpory pro konkrétní řešení na webu Marketplace. Nebo mohou být publikovány pro účely verze Preview a testování. Můžou se kdykoli změnit nebo odebrat bez upozornění. Nepoužívejte je, pokud je jejich přítomnost veřejně dokumentována společností Microsoft nebo Red Hat.

## <a name="publishing-policy"></a>Zásady publikování

Obrázky aktualizací Microsoft a Red Hat jako nové podverze se uvolňují podle požadavků na konkrétní běžné chyby zabezpečení a hrozby (CVEs) nebo příležitostné změny nebo aktualizace konfigurace. Usilujeme o poskytování aktualizovaných imagí co nejdříve během tří pracovních dnů po vydání nebo dostupnosti CVE opravy.

Aktualizujeme jenom aktuální dílčí verzi v dané rodině imagí. S vydáním novější dílčí verze zastavíme aktualizaci starší podverze. Například s vydáním RHEL 7,6 se již neaktualizují image RHEL 7,5.

>[!NOTE]
> Aktivní virtuální počítače Azure zřízené z RHEL imagí s průběžnými platbami jsou připojené ke službě Azure RHUI a můžou dostávat aktualizace a opravy hned po vydání Red Hat a replikovat do Azure RHUI. Načasování je obvykle méně než 24 hodin po oficiální verzi Red Hat. Tyto virtuální počítače pro získání aktualizací nevyžadují novou publikovanou image. Zákazníci mají plnou kontrolu nad tím, kdy se má aktualizace zahájit.

## <a name="image-retention-policy"></a>Zásady uchovávání imagí

Aktuální zásada zachová všechny dřív publikované image. Vyhrazujeme si právo odebrat obrázky, u kterých se říká, že způsobují problémy jakéhokoli druhu. Například Image s nesprávnými konfiguracemi z důvodu pozdější aktualizace platformy nebo součásti mohou být odebrány. Image, které by se mohly odebrat, se řídí zásadami aktuální Azure Marketplace a poskytují oznámení až 30 dnů před odebráním obrázku.

## <a name="next-steps"></a>Další kroky

* Pokud chcete zobrazit úplný seznam imagí RHEL v Azure, přečtěte si část [Red Hat Enterprise Linux (RHEL) imagí dostupné v Azure](./redhat-imagelist.md).
* Další informace o infrastruktuře aktualizací Red Hat najdete v tématu [infrastruktura aktualizace Red Hat pro virtuální počítače RHEL na vyžádání v Azure](./redhat-rhui.md).
* Další informace o nabídce BYOS pro RHEL Red Hat Enterprise Linux najdete v článku o tom, jak se v Azure naučíte používat [zlaté image pro vlastní odběry](./byos.md).
* Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete v tématu [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata).
