---
title: Red Hat Enterprise Linux imagí v Azure | Microsoft Docs
description: Přečtěte si o Red Hat Enterprise Linuxch imagí v Microsoft Azure
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
ms.openlocfilehash: 5ed5d9337dd4e7acdbba25c4cb66d2690793f250
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134397"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Přehled Red Hat Enterprise Linuxch imagí
Tento článek popisuje dostupné image Red Hat Enterprise Linux (RHEL) v Azure Marketplace společně se zásadami týkajícími se jejich pojmenování a uchovávání.

Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) . Podrobnosti o cenách najdete v [cenové kalkulačkě Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> RHEL image, které jsou aktuálně dostupné na webu Azure Marketplace, podporují licenční modely BYOS (Přineste si vlastní předplatné) nebo s průběžnými platbami (PAYG). [Zvýhodněné hybridní využití Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) a dynamické přepínání mezi BYOS a PAYG se nepodporuje. Přepnutí režimu licencí vyžaduje opětovné nasazení virtuálního počítače z odpovídající image.

>[!NOTE]
> Pro všechny problémy související s imagemi RHEL na webu Azure Marketplace prosím Dodejte lístek podpory společnosti Microsoft.

## <a name="viewing-images-available-in-azure"></a>Zobrazení imagí dostupných v Azure
Při hledání "Red Hat" na webu Marketplace nebo při vytváření prostředku v Azure Portal uživatelském rozhraní se zobrazí pouze podmnožina všech dostupných imagí RHEL. Úplnou sadu dostupných imagí virtuálních počítačů můžete kdykoli získat pomocí Azure CLI/PowerShellu nebo rozhraní API.

Pokud chcete zobrazit úplnou sadu dostupných imagí Red Hat v Azure, spusťte následující příkaz.

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Konvence pojmenování
Image virtuálních počítačů v Azure jsou seřazené podle vydavatele, nabídky, SKU a verze. Kombinace vydavatele: nabídka: SKU: verze je identifikátor URN image a jednoznačně identifikuje obrázek, který se má použít.

`RedHat:RHEL:7-LVM:7.6.2018103108` například odkazuje na bitovou kopii RHEL 7,6 LVM-partition vytvořenou 31. října 2018.

Ukázka, jak vytvořit virtuální počítač s RHEL 7,6, je uvedená níže.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>Moniker "poslední"
Azure REST API umožňuje místo konkrétní verze použít moniker "nejnovější" verze. Když použijete "nejnovější", zřídí se nejnovější dostupná image pro daného vydavatele, nabídku a SKU.

`RedHat:RHEL:7-LVM:latest` například odkazuje na nejnovější dostupnou bitovou kopii RHEL 7 Family LVM-Partitioned.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> Obecně platí, že porovnání verzí za účelem určení nejnovější následuje pravidla [metody CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).
Porovnání verzí tohoto obrázku je provedeno porovnáním hodnot jako objektu [verze](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) – ne jako řetězce.

## <a name="rhel-6-image-types"></a>Typy imagí RHEL 6
Pro image RHEL 6. x jsou typy obrázků následující:

|Vydavatel | Nabídka | Hodnota SKU | Verze | Podrobnosti
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Dílčí verze (např. 6,9) | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 6.9.2018010506) | Všechny standardní image RHEL 6. x se řídí touto konvencí.
|RedHat | RHEL – BYOS | RHEL – raw69 | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 6.9.20181023) | Tato Image je image RHEL 6,9 BYOS.
|RedHat | RHEL | RHEL-SAP-APPS | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 6.8.2017053118) | Jedná se o image RHEL 6,8 pro aplikace SAP. Má oprávnění k přístupu k úložištím aplikací SAP i k základním úložištím RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 6.7.2017053121) | Toto je RHEL 6,7 pro obrázek SAP HANA. Má oprávnění k přístupu k SAP HANAm úložištím i k základním úložištím RHEL.

## <a name="rhel-7-image-types"></a>RHEL 7 – typy obrázků
Pro image RHEL 7. x existuje několik různých typů obrázků. V následující tabulce jsou uvedeny různé sady imagí, které nabízíme. Úplný seznam můžete zobrazit pomocí příkazu AZ CLI `az vm image list --publisher redhat --all`.

>[!NOTE]
> Pokud není uvedeno jinak, všechny image jsou LVM rozdělené a budou se připojovat k běžným RHEL úložištím (tj. ne EUS, ne E4S). Až budeme přesměrováni na publikování pouze imagí s LVM oddíly, ale otevře se pro zpětnou vazbu k tomuto rozhodnutí. Podrobnosti o podpoře rozšířené aktualizace a službách aktualizace pro SAP jsou k dispozici na [stránce Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata).

|Vydavatel | Nabídka | Hodnota SKU | Verze | Podrobnosti
|----------|-------|------------|---------|--------
|RedHat | RHEL | Dílčí verze (např. 7,6) | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 7.6.2019102813) | Image publikované před dubna 2019 budou připojené ke standardním úložištím RHEL. Obrázky publikované po dubna 2019 budou připojené k úložištím rozšířené aktualizace (EUS) Red Hat, aby bylo možné uzamknout určitou dílčí verzi. Zákazníci, kteří chtějí pravidelná úložiště, by měli použít image obsahující 7-LVM nebo 7-RAW v hodnotě SKU (podrobnosti níže). RHEL 7,7 a novější obrázky budou LVM – rozdělené do oddílů. Všechny ostatní obrázky v této kategorii jsou nezpracované – oddíly.
|RedHat | RHEL | 7 – RAW | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 7.6.2019102813) | Tyto image jsou nezpracované – rozdělené do oddílů (tj. nepřidaly se žádné logické svazky).
|RedHat | RHEL | 7-RAW-CI | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 7.6.2019072418) | Tyto image jsou nezpracované (tj. nepřidaly se žádné logické svazky) a použijí pro zřizování Cloud-init.
|RedHat | RHEL | 7 – LVM | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 7.6.2019062414) | Tyto image jsou LVM-Partitioned.
|RedHat | RHEL – BYOS | RHEL-{LVM, RAW} | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 7.7.20190819) | Tyto image jsou image BYOS v RHEL 7. Nejsou připojené k žádným úložištím a poplatky za RHEL Premium se neúčtují. Pokud vás zajímá image RHEL BYOS, [požádejte o přístup](https://aka.ms/rhel-byos). Hodnoty SKU končí vedlejší verzí a označují, zda je obrázek RAW nebo LVM-Partitioned. Například hodnota SKU RHEL-lvm77 označuje obrázek RHEL 7,7 s LVM oddíly.
|RedHat | RHEL | RHEL-SAP | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 7.6.2019071300) | Tyto image jsou RHEL pro Image SAP. Mají oprávnění pro přístup k úložištím SAP HANA a aplikací a také k úložištím E4S v RHEL. Účtování zahrnuje RHEL Premium a SAP Premium nad základním výpočetním poplatkem.
|RedHat | RHEL | RHEL-SAP-HA | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 7.6.2019062320) | Tyto image jsou RHEL pro SAP s imagí pro vysokou dostupnost a službu Update Services. Mají oprávnění k přístupu k úložištím SAP HANA a aplikací a k úložištím s vysokou dostupností a také k úložištím RHEL E4S. Fakturace zahrnuje RHEL Premium, SAP Premium a HA Premium nad základním výpočetním poplatkem.
|RedHat | RHEL | RHEL-HA | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 7.6.2019062019) | Jedná se o RHEL obrázky, které mají také oprávnění k přístupu k doplňku vysoké dostupnosti. V důsledku doplňku HA úrovně Premium se za RHEL a základní výpočetní poplatky účtují trochu víc.
|RedHat | RHEL | RHEL-SAP-APPS | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 7.3.2017053118) | Tyto image jsou zastaralé, protože aplikace SAP a SAP HANA úložišť byly zkombinovány do úložišť SAP. Toto jsou RHEL pro Image aplikací SAP. Mají oprávnění k přístupu k úložištím aplikací SAP i k základním úložištím RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 7.3.2018051421) | Tyto image jsou zastaralé, protože aplikace SAP a SAP HANA úložišť byly zkombinovány do úložišť SAP. Toto jsou RHEL for SAP HANA obrázky. Mají oprávnění pro přístup k SAP HANA úložišť i k základním úložištím RHEL.

## <a name="rhel-8-image-types"></a>Typy imagí RHEL 8
Podrobnosti o typech imagí RHEL 8 jsou uvedené níže.

|Vydavatel | Nabídka | Hodnota SKU | Verze | Podrobnosti
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 8.0.20191023) | Tyto image jsou image RHEL 8,0 LVM-dělené bitové kopie připojené ke standardním úložištím Red Hat.
|RedHat | RHEL | 8 – Gen2 | Zřetězené hodnoty vedlejší verze RHEL a datum publikování (např. 8.0.20191024) | Tyto image jsou Hyper-V Generation 2 RHEL 8,0 LVM-dělené image připojené ke standardním úložištím Red Hat. Další informace o virtuálních počítačích 2. generace v Azure [najdete tady](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2).

## <a name="rhel-longer-support-add-ons"></a>RHEL už podporuje doplňky.

### <a name="extended-update-support-eus"></a>Podpora rozšířené aktualizace (EUS)
Od dubna 2019 jsou k dispozici RHEL image, které jsou ve výchozím nastavení připojeny k úložištím rozšířené aktualizace (EUS). Další podrobnosti o RHEL EUS najdete v [dokumentaci k Red Hat](https://access.redhat.com/articles/rhel-eus).

Přepínání na úložiště EUS je možné a podporuje se. Pokyny k přepnutí virtuálního počítače na EUS a další podrobnosti o datech konce životnosti EUS jsou k dispozici [zde](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS se v RHEL Extras nepodporuje. To znamená, že pokud instalujete balíček, který je obvykle dostupný z kanálu RHEL Extras, nebudete ho moct v EUS použít. Životní cyklus produktu Red Hat Extras je [zde](https://access.redhat.com/support/policy/updates/extras/)podrobně popsán.

#### <a name="differentiating-between-regular-and-eus-images"></a>Rozlišení mezi běžnými a EUS obrázky.
Zákazníci, kteří chtějí používat image připojené k úložištím EUS, by měli použít image RHEL, která obsahuje číslo podverze RHEL v SKU.

Můžete například zobrazit následující dvě dostupné image RHEL 7,4:
```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```
V tomto případě se `RedHat:RHEL:7.6:7.6.2019102813` k úložištím EUS ve výchozím nastavení připojí (hodnota SKU 7,4) a `RedHat:RHEL:7-LVM:7.6.2019062414` bude ve výchozím nastavení přiřazená k EUS úložištím (hodnota SKU 7 – LVM).

Pokud chcete používat pravidelná úložiště (bez EUS), nasaďte je pomocí Image, která v SKU neobsahuje číslo dílčí verze.

#### <a name="rhel-images-with-eus"></a>Image RHEL s EUS
Následující tabulka bude platit pro image RHEL připojené k úložištím EUS.

>[!NOTE]
> V době psaní mají EUS podporu jenom RHEL 7,4 a novější podverze. EUS se už nepodporuje pro RHEL < = 7,3.
>
> Další podrobnosti o dostupnosti RHEL EUS najdete [tady](https://access.redhat.com/support/policy/updates/errata).

Dílčí verze |Příklad obrázku EUS              |Stav EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat: RHEL: 7.4:7.4.2019041718 | Obrázky publikované v dubnu 2019 a novějších budou ve výchozím nastavení EUS|
RHEL 7.5      |RedHat: RHEL: 7.5:7.5.2019060305 | Obrázky publikované od června 2019 a novějších budou ve výchozím nastavení EUS |
RHEL 7,6      |RedHat: RHEL: 7.6:7.6.2019052206 | Publikované obrázky můžou 2019 a novější EUS ve výchozím nastavení.  |
RHEL 8.0      |Není k dispozici                            | Není dostupný žádný EUS ze Red Hat.                               |

### <a name="update-services-for-sap-e4s"></a>Služba Update Services pro SAP (E4S)
Nejnovější image RHEL for SAP budou připojeny ke službám Update Services for SAP Solutions Subscriptions (E4S). Další podrobnosti o E4S najdete v [dokumentaci](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)k Red Hat.

#### <a name="rhel-images-with-e4s"></a>Image RHEL s E4S
Obrázky z následujících nabídek vytvořené po prosince 2019 budou připojené k úložištím E4S.

* RHEL-SAP (RHEL pro SAP)
* RHEL-SAP-HA (RHEL for SAP s HA a Update Services)

## <a name="other-available-offers-and-skus"></a>Další dostupné nabídky a SKU
Úplný seznam dostupných nabídek a SKU může zahrnovat další obrázky nad rámec toho, co je uvedeno v tabulce výše, například `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Tyto nabídky se dají použít k poskytování podpory pro konkrétní řešení na webu Marketplace nebo můžou být publikované pro účely verze Preview a testování. Můžou se kdykoli změnit nebo odebrat bez upozornění. Nepoužívejte je v případě, že jejich přítomnost není veřejně dokumentována společností Microsoft nebo Red Hat.

## <a name="publishing-policy"></a>Zásady publikování
Obrázky aktualizací Microsoft a Red Hat jako nové podverze se uvolňují podle požadavků na konkrétní CVEs nebo pro příležitostné změny nebo aktualizace konfigurace. Usilujeme o poskytování aktualizovaných imagí co nejdříve – do tří pracovních dnů po vydání nebo dostupnosti CVE opravy.

Aktualizujeme jenom aktuální dílčí verzi v dané rodině imagí. S vydáním novější dílčí verze zastavíme aktualizaci starší podverze. Například s vydáním RHEL 7,6 se již nebudete muset aktualizovat image RHEL 7,5.

>[!NOTE]
> Aktivní virtuální počítače Azure zřízené z RHEL imagí s průběžnými platbami jsou připojené ke službě Azure RHUI a můžou dostávat aktualizace a opravy hned po vydání Red Hat a replikovat do Azure RHUI (obvykle během méně než 24 hodin po oficiální verzi Red Hat). . Tyto virtuální počítače nevyžadují novou publikovanou image pro získání aktualizací a zákazníci mají plnou kontrolu nad tím, kdy se má tato aktualizace zahájit.

## <a name="image-retention-policy"></a>Zásady uchovávání imagí
Naše aktuální zásada zachová všechny dříve publikované image. Vyhrazujeme si právo odebrat obrázky, u kterých se říká, že způsobují problémy jakéhokoli druhu. Například Image s nesprávnou konfigurací z důvodu pozdější aktualizace platformy nebo součásti mohou být odebrány. Obrázky, které se dají odebrat, se budou řídit aktuálními zásadami Marketplace a oznámení se budou zobrazovat až po dobu 30 dnů před odebráním obrázku.

## <a name="next-steps"></a>Další kroky
* Podívejte se na úplný seznam [imagí RHEL v Azure](./redhat-imagelist.md).
* Přečtěte [si další informace o infrastruktuře aktualizací](https://aka.ms/rhui-update)Red Hat pro Azure.
* Přečtěte si další informace o [nabídce RHEL BYOS](./byos.md).
* Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .
