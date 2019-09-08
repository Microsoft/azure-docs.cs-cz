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
ms.date: 8/14/2019
ms.author: borisb
ms.openlocfilehash: c11ce31913baa8c638e94bdf92ef622cd8899e03
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764307"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Red Hat Enterprise Linux imagí v Azure
Tento článek popisuje dostupné image Red Hat Enterprise Linux (RHEL) v Azure Marketplace společně se zásadami týkajícími se jejich pojmenování a uchovávání.

Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .

>[!Important]
> RHEL image, které jsou aktuálně dostupné na webu Azure Marketplace, podporují licenční modely BYOS (Přineste si vlastní předplatné) nebo s průběžnými platbami (PAYG). [Zvýhodněné hybridní využití Azure](../windows/hybrid-use-benefit-licensing.md) a dynamické přepínání mezi BYOS a PAYG se nepodporuje. Přepnutí režimu licencí vyžaduje opětovné nasazení virtuálního počítače z odpovídající image.

>[!Note]
> Všechny problémy související s imagemi RHEL v galerii Azure Marketplace najdete v části lístek podpory společnosti Microsoft.

## <a name="images-available-in-the-ui"></a>Obrázky dostupné v uživatelském rozhraní
Při hledání "Red Hat" na webu Marketplace nebo při vytváření prostředku v Azure Portal uživatelském rozhraní se zobrazí podmnožina dostupných imagí RHEL a souvisejících produktů Red Hat. Úplnou sadu dostupných imagí virtuálních počítačů můžete kdykoli získat pomocí Azure CLI/PowerShellu nebo rozhraní API.

Pokud chcete zobrazit úplnou sadu dostupných imagí Red Hat v Azure, spusťte následující příkaz.

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Konvence pojmenování
Image virtuálních počítačů v Azure jsou seřazené podle vydavatele, nabídky, SKU a verze. Kombinace vydavatele: nabídka: SKU: verze je identifikátor URN image a jednoznačně identifikuje obrázek, který se má použít.

Například `RedHat:RHEL:7-RAW:7.6.2018103108` odkazuje na bitovou kopii RHEL 7,6 RAW-partition vytvořenou 31. října 2018.

Ukázka, jak vytvořit virtuální počítač s RHEL 7,6, je uvedená níže.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>Moniker "poslední"
Azure REST API povoluje použití monikeru "nejnovější" pro verzi místo konkrétní verze. Když použijete "nejnovější", zřídí se nejnovější dostupná image pro daného vydavatele, nabídku a SKU.

Například `RedHat:RHEL:7-RAW:latest` odkazuje na nejnovější dostupnou bitovou kopii RHEL 7 s nezpracovanými oddíly.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> Obecně platí, že porovnání verzí za účelem určení nejnovější následuje pravidla [metody CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Současná konvence vytváření názvů
Všechny aktuálně publikované image RHEL používají model průběžných plateb a jsou [v Azure připojení k infrastruktuře Red Hat Update Infrastructure (RHUI)](https://aka.ms/rhui-update). Pro image RHEL 7 se přijala nová konvence pojmenování, ve které je místo verze zadané schéma dělení na disk (RAW, LVM). Verze image RHEL bude obsahovat buď 7-RAW, nebo 7-LVM. V tuto chvíli se nezmění pojmenování řady RHEL 6.

V této konvenci pojmenování budou 2 typy SKU imagí RHEL 7: SKU, které uvádějí podverzi a SKU, které ne. Pokud chcete použít 7-RAW nebo 7-LVM SKU, můžete zadat podverzi RHEL, kterou chcete v této verzi nasadit. Pokud se rozhodnete "nejnovější" verzi, bude se zřídit nejnovější dílčí verze RHEL.

>[!NOTE]
> Ve RHEL pro SAP sada imagí zůstane verze RHEL opravená. V takovém případě jejich konvence vytváření názvů zahrnuje konkrétní verzi v SKU.

>[!NOTE]
> Sada imagí RHEL 6 nebyla přesunuta do nových zásad vytváření názvů.

## <a name="extended-update-support-eus"></a>Podpora rozšířené aktualizace (EUS)
Od dubna 2019 jsou k dispozici RHEL image, které jsou ve výchozím nastavení připojeny k úložištím rozšířené aktualizace (EUS). Další podrobnosti o RHEL EUS najdete v [dokumentaci k Red Hat](https://access.redhat.com/articles/rhel-eus).

Pokyny k přepnutí virtuálního počítače na EUS a další podrobnosti o datech konce životnosti EUS jsou k dispozici [zde](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS se v RHEL Extras nepodporuje. To znamená, že pokud instalujete balíček, který je obvykle dostupný z kanálu RHEL Extras, nebudete ho moct v EUS použít. Životní cyklus produktu Red Hat Extras je [zde](https://access.redhat.com/support/policy/updates/extras/)podrobně popsán.

### <a name="for-customers-that-want-to-use-eus-images"></a>Pro zákazníky, kteří chtějí používat EUS Image:
Zákazníci, kteří chtějí používat image připojené k úložištím EUS, by měli použít image RHEL, která obsahuje číslo podverze RHEL v SKU. Tyto obrázky budou nezpracované – rozdělené do oddílů (tj. ne LVM).

Můžete například zobrazit následující 2 dostupné image RHEL 7,4:
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
V tomto případě `RedHat:RHEL:7.4:7.4.2019041718` se ve výchozím nastavení připojí k úložištím EUS a `RedHat:RHEL:7-RAW:7.4.2018010506` ve výchozím nastavení se připojí k úložištím bez EUS.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>Pro zákazníky, kteří nechtějí používat image EUS:
Pokud nechcete použít image, která je ve výchozím nastavení připojená k EUS, nasaďte ji pomocí Image, která v SKU neobsahuje číslo dílčí verze.

#### <a name="rhel-images-with-eus"></a>Image RHEL s EUS
Následující tabulka bude platit pro image RHEL, které obsahují podverze v SKU.

>[!NOTE]
> V době psaní mají EUS podporu jenom RHEL 7,4 a novější podverze. EUS se už nepodporuje pro RHEL < = 7,3.
>
> Další podrobnosti o dostupnosti RHEL EUS najdete [tady](https://access.redhat.com/support/policy/updates/errata).

Dílčí verze |Příklad obrázku EUS              |Stav EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat: RHEL: 7.4:7.4.2019041718 | Obrázky publikované v dubnu 2019 a novějších budou ve výchozím nastavení EUS|
RHEL 7.5      |RedHat: RHEL: 7.5:7.5.2019060305 | Obrázky publikované od června 2019 a novějších budou ve výchozím nastavení EUS |
RHEL 7,6      |RedHat: RHEL: 7.6:7.6.2019052206 | Publikované obrázky můžou 2019 a novější EUS ve výchozím nastavení.  |
RHEL 8.0      |Není k dispozici                            | Není dostupný žádný EUS ze Red Hat.                               |


## <a name="list-of-rhel-images-available"></a>Seznam dostupných imagí RHEL
Pro obecné použití jsou momentálně dostupné tyto nabídky:

Nabídka| SKU | Dělení | Zřizování | Poznámky
:----|:----|:-------------|:-------------|:-----
RHEL          | 7 – RAW    | ZÍSKÁNÍ    | Linuxový agent | RHEL 7. x rodina imagí. <br> Nepřipojeno k úložištím EUS ve výchozím nastavení.
|             | 7 – LVM    | LVM    | Linuxový agent | RHEL 7. x rodina imagí. <br> Nepřipojeno k úložištím EUS ve výchozím nastavení.
|             | 7-RAW-CI | RAW-CI | Cloud-init  | RHEL 7. x rodina imagí. <br> Nepřipojeno k úložištím EUS ve výchozím nastavení.
|             | 6.7      | ZÍSKÁNÍ    | Linuxový agent |
|             | 6.8      | ZÍSKÁNÍ    | Linuxový agent |
|             | 6.9      | ZÍSKÁNÍ    | Linuxový agent |
|             | 6.10     | ZÍSKÁNÍ    | Linuxový agent |
|             | 7.2      | ZÍSKÁNÍ    | Linuxový agent |
|             | 7.3      | ZÍSKÁNÍ    | Linuxový agent |
|             | 7.4      | ZÍSKÁNÍ    | Linuxový agent | Připojí se k úložištím EUS ve výchozím nastavení od dubna 2019.
|             | 7.5      | ZÍSKÁNÍ    | Linuxový agent | Připojeno k úložištím EUS ve výchozím nastavení od června 2019.
|             | 7,6      | ZÍSKÁNÍ    | Linuxový agent | Připojeno k úložištím EUS ve výchozím nastavení jako květen 2019.
|             | 7,7      | LVM    | Linuxový agent | Ve výchozím nastavení připojené k úložištím EUS.
RHEL-SAP      | 7.4      | LVM    | Linuxový agent | RHEL 7,4 pro SAP HANA a obchodní aplikace
|             | 7.5      | LVM    | Linuxový agent | RHEL 7,5 pro SAP HANA a obchodní aplikace
RHEL-SAP-HANA | 6.7      | ZÍSKÁNÍ    | Linuxový agent | RHEL 6,7 pro SAP HANA
|             | 7.2      | LVM    | Linuxový agent | RHEL 7,2 pro SAP HANA
|             | 7.3      | LVM    | Linuxový agent | RHEL 7,3 pro SAP HANA
RHEL-SAP-APPS | 6.8      | ZÍSKÁNÍ    | Linuxový agent | RHEL 6,8 pro SAP Business Applications
|             | 7.3      | LVM    | Linuxový agent | RHEL 7,3 pro SAP Business Applications
RHEL-HA       | 7.4      | LVM    | Linuxový agent | RHEL 7,4 s doplňkem HA
|             | 7.5      | LVM    | Linuxový agent | RHEL 7,5 s doplňkem HA
|             | 7,6      | LVM    | Linuxový agent | RHEL 7,6 s doplňkem HA
RHEL-SAP-HA   | 7.4      | LVM    | Linuxový agent | RHEL 7,4 pro SAP s doplňkem HA
|             | 7.5      | LVM    | Linuxový agent | RHEL 7,5 pro SAP s doplňkem HA
|             | 7,6      | LVM    | Linuxový agent | RHEL 7,6 pro SAP s doplňkem HA

### <a name="old-naming-convention"></a>Stará konvence vytváření názvů
Řada imagí RHEL 7 a RHEL 6 rodina imagí používala ve svých SKU konkrétní verze, až do výše popsané změny konvence pojmenování.

Číselné jednotky SKU najdete v seznamu úplných imagí. Microsoft a Red Hat vytvoří nové číselné SKU, když se objeví nová dílčí verze.

### <a name="other-available-offers-and-skus"></a>Další dostupné nabídky a SKU
Úplný seznam dostupných nabídek a SKU může zahrnovat další obrázky nad rámec toho, co je uvedeno v tabulce výše, `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`například. Tyto nabídky se dají použít k poskytování podpory pro konkrétní řešení na webu Marketplace nebo můžou být publikované pro účely verze Preview a testování. Můžou se kdykoli změnit nebo odebrat bez upozornění. Nepoužívejte je v případě, že jejich přítomnost není veřejně dokumentována společností Microsoft nebo Red Hat.

## <a name="publishing-policy"></a>Zásady publikování
Obrázky aktualizací Microsoft a Red Hat jako nové podverze se uvolňují podle požadavků na konkrétní CVEs nebo pro příležitostné změny nebo aktualizace konfigurace. Usilujeme o poskytování aktualizovaných imagí co nejdříve – do tří pracovních dnů po vydání nebo dostupnosti CVE opravy.

Aktualizujeme jenom aktuální dílčí verzi v dané rodině imagí. S vydáním novější dílčí verze zastavíme aktualizaci starší podverze. Například s vydáním RHEL 7,6 se již nebudete muset aktualizovat image RHEL 7,5.

>[!NOTE]
> Aktivní virtuální počítače Azure zřízené z RHEL imagí s průběžnými platbami jsou připojené ke službě Azure RHUI a můžou dostávat aktualizace a opravy hned po vydání Red Hat a replikovat do Azure RHUI (obvykle během méně než 24 hodin po oficiální verzi Red Hat). . Tyto virtuální počítače nevyžadují novou publikovanou image pro získání aktualizací a zákazníci mají plnou kontrolu nad tím, kdy se má tato aktualizace zahájit.

## <a name="image-retention-policy"></a>Zásady uchovávání imagí
Naše aktuální zásada zachová všechny dříve publikované image. Vyhrazujeme si právo odebrat obrázky, u kterých se říká, že způsobují problémy jakéhokoli druhu. Například Image s nesprávnou konfigurací z důvodu pozdější aktualizace platformy nebo součásti mohou být odebrány. Obrázky, které se dají odebrat, se budou řídit aktuálními zásadami Marketplace a oznámení se budou zobrazovat až po dobu 30 dnů před odebráním obrázku.

## <a name="next-steps"></a>Další kroky
* Přečtěte [si další informace o infrastruktuře aktualizací](https://aka.ms/rhui-update)Red Hat pro Azure.
* Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .
