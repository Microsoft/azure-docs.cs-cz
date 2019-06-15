---
title: Image Red Hat Enterprise Linux v Azure | Dokumentace Microsoftu
description: Seznamte se s imagí Red Hat Enterprise Linux v Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: b40f62a90dbe7c822b95476abe6ec25cf3fb21d6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070030"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Image Red Hat Enterprise Linux v Azure
Tento článek popisuje dostupných imagí Red Hat Enterprise Linux (RHEL) na webu Azure Marketplace spolu s zásady po jejich názvy a uchování.

Informace o zásadách podpory Red Hat pro všechny verze systému RHEL můžete najít na [Red Hat Enterprise Linux životního cyklu](https://access.redhat.com/support/policy/updates/errata) stránky.

>[!Important]
> Image RHEL aktuálně dostupné v Tržišti Azure marketplace podporu přenést-Your-vlastní – předplatné (BYOS) nebo průběžných plateb (PAYG) licenční modely. [Program Azure Hybrid Use Benefit](../windows/hybrid-use-benefit-licensing.md) a dynamické přepínání mezi BYOS a průběžné platby se nepodporuje. Přepnout režim licencování vyžaduje opětovné nasazení virtuálního počítače z odpovídající image.

>[!Note]
> Pro jakýkoli problém související s imagí RHEL v galerii Azure marketplace prosím lístek podpory s Microsoftem.

## <a name="images-available-in-the-ui"></a>Image dostupné v uživatelském rozhraní
Při hledání "Red Hat" na webu Marketplace nebo při vytváření prostředku v uživatelském rozhraní webu Azure portal, uvidíte podmnožinu dostupných imagí RHEL a související produkty Red Hat. Vždy můžete získat úplnou sadu dostupných imagí virtuálních počítačů pomocí Azure CLI/Powershellu/API.

Pokud chcete zobrazit úplnou sadu dostupných imagí Red Hat v Azure, spusťte následující příkaz

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Zásady vytváření názvů
Image virtuálních počítačů v Azure jsou uspořádané podle vydavatele, nabídky, SKU a verze. Kombinace vydavatele: nabídka: SKU:Version je obrázek URN a jednoznačně identifikuje obrázek, který se použije.

Například `RedHat:RHEL:7-RAW:7.6.2018103108` odkazuje nezpracovaná oddílů Image RHEL 7.6 založená na 31. října 2018.

Ukázku toho, jak vytvořit virtuální počítač s RHEL 7.6 je uveden níže.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>"Posledního" monikeru
Rozhraní Azure REST API umožňuje používat zástupný název "nejnovější" pro verzi místo konkrétní verzi. Použití "nejnovější" bude poskytovat nejnovější dostupné image pro daného vydavatele, nabídky a skladové položky.

Například `RedHat:RHEL:7-RAW:latest` odkazuje na nejnovější RHEL 7 řady nezpracovaná oddílů image k dispozici.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> Obecně platí, následuje porovnání verze k určení nejnovějšího pravidel [metoda CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Aktuální zásady vytváření názvů
Všechny aktuálně publikovaných imagí RHEL použít model průběžných plateb a jsou připojené k [Red Hat Update Infrastructure (RHUI) v Azure](https://aka.ms/rhui-update). Byla přijata novým zásadám vytváření názvů pro řady imagí RHEL 7, ve kterých se schéma dělení disku (nezpracovaná, LVM) je zadán v SKU namísto verze. Verze image RHEL bude obsahovat buď 7 RAW nebo 7 LVM. V tuto chvíli nebylo změněno řady systému RHEL 6 pojmenování.

Bude existovat 2 typy image RHEL 7 SKU v tyto zásady vytváření názvů: Skladové položky, které uvádějí podverze a skladové položky, které ji nemají. Pokud chcete použít 7-RAW nebo 7 LVM SKU, můžete zadat podverze RHEL, kterou chcete nasadit ve verzi. Pokud se rozhodnete "posledního" verze, bude vytvořena nejnovější dílčí verzi RHEL.

>[!NOTE]
> RHEL for SAP sadu bitových kopií zůstane pevné verzi RHEL. V důsledku toho jejich zásady vytváření názvů obsahuje konkrétní verzi ve skladové Položce.

>[!NOTE]
> RHEL 6 sadu bitových kopií nepřesunula k novým zásadám vytváření názvů.

## <a name="extended-update-support-eus"></a>Aktualizace rozšířené podpory (EUS)
Jako Image RHEL. 2019. dubna, jsou k dispozici, které jsou připojeny k úložišť rozšířenou podporu aktualizace (EUS) ve výchozím nastavení. Další podrobnosti o RHEL EUS jsou k dispozici v [dokumentace systému Red Hat](https://access.redhat.com/articles/rhel-eus).

Pokyny o tom, jak přepnout virtuální počítač na EUS a další podrobnosti o EUS podporu ukončenou životností data jsou k dispozici [tady](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS nepodporuje funkce RHEL. To znamená, že při instalaci balíčku, který je obvykle k dispozici z kanálu funkce RHEL, nebudete mít k tomu, když jste na EUS. Životní cyklus Red Hat funkce produktu je podrobně popsán [tady](https://access.redhat.com/support/policy/updates/extras/).

### <a name="for-customers-that-want-to-use-eus-images"></a>Pro zákazníky, kteří chtějí používat EUS imagí:
Zákazníci, které chcete použít Image, které jsou připojeny k úložištím EUS měli použít image RHEL, který obsahuje číslo podverze RHEL ve skladové Položce. Tyto Image bude nezpracovaná oddílů (tedy ne LVM).

Například se může zobrazit následující 2 RHEL 7.4 dostupných imagí:
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
V takovém případě `RedHat:RHEL:7.4:7.4.2019041718` bude připojen k úložištím EUS ve výchozím nastavení, a `RedHat:RHEL:7-RAW:7.4.2018010506` budou připojena k jiné EUS úložišť ve výchozím nastavení.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>Pro zákazníky, které nechcete použít EUS imagí:
Pokud nechcete použít obraz, který je připojený k EUS ve výchozím nastavení, nasazení, můžete použít bitovou kopii, která neobsahuje číslo podverze ve skladové Položce.

#### <a name="rhel-images-with-eus"></a>Image RHEL s EUS
V následující tabulce platí pro Image RHEL, které obsahují podverze ve skladové Položce.

>[!NOTE]
> V době psaní mají EUS podporují pouze RHEL 7.4 a pozdější podverze. EUS už není podporovaná pro RHEL < = 7.3.

Podverze |Obrázek EUS – příklad              |Stav EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | Imagí publikovaných dubna 2019 a později bude EUS ve výchozím nastavení|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Imagí publikovaných 2019 dne a později bude EUS ve výchozím nastavení |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | Obrázky publikovaná. května 2019 a později bude EUS ve výchozím nastavení  |
RHEL 8.0      |neuvedeno                            | Žádné EUS aktuálně aktuálně dostupných imagí                 |


## <a name="list-of-rhel-images-available"></a>Seznam dostupných imagí RHEL
Tyto nabídky jsou že skladové položky jsou aktuálně k dispozici pro obecné použití:

Nabídka| Skladová jednotka (SKU) | Dělení | Zřizování | Poznámky
:----|:----|:-------------|:-------------|:-----
RHEL          | NEZPRACOVANÁ 7    | RAW    | Linuxový agent | RHEL 7 řady imagí. <br> Ve výchozím nastavení nejsou připojené k EUS úložišť.
|             | 7-LVM    | LVM    | Linuxový agent | RHEL 7 řady imagí. <br> Ve výchozím nastavení nejsou připojené k EUS úložišť.
|             | 7-RAW-CI | RAW-CI | Cloud-init  | RHEL 7 řady imagí. <br> Ve výchozím nastavení nejsou připojené k EUS úložišť.
|             | 6.7      | RAW    | Linuxový agent | Image RHEL 6.7, staré pojmenování
|             | 6.8      | RAW    | Linuxový agent | Jak je uvedeno výše pro RHEL 6.8 stejný
|             | 6.9      | RAW    | Linuxový agent | Jak je uvedeno výše pro RHEL 6.9 stejný
|             | 6.10     | RAW    | Linuxový agent | Jak je uvedeno výše pro RHEL 6.10 stejný
|             | 7.2      | RAW    | Linuxový agent | Stejný, jak je uvedeno výše pro RHEL 7.2
|             | 7.3      | RAW    | Linuxový agent | Jak je uvedeno výše pro RHEL 7.3 stejný
|             | 7.4      | RAW    | Linuxový agent | Jak je uvedeno výše pro RHEL 7.4 stejný. <br> Připojit k úložištím EUS ve výchozím nastavení od dubna 2019
|             | 7.5      | RAW    | Linuxový agent | Jak je uvedeno výše pro RHEL 7.5 stejný. <br> Připojit k úložištím EUS ve výchozím nastavení od června 2019
|             | 7.6      | RAW    | Linuxový agent | Jak je uvedeno výše pro RHEL 7.6 stejný. <br> Připojit k úložištím EUS ve výchozím nastavení od května 2019
RHEL-SAP      | 7.4      | LVM    | Linuxový agent | 7\.4 RHEL for SAP HANA a obchodní aplikace
|             | 7.5      | LVM    | Linuxový agent | 7\.5 RHEL for SAP HANA a obchodní aplikace
RHEL-SAP-HANA | 6.7      | RAW    | Linuxový agent | RHEL 6.7 pro SAP HANA
|             | 7.2      | LVM    | Linuxový agent | RHEL 7.2 pro SAP HANA
|             | 7.3      | LVM    | Linuxový agent | 7\.3 RHEL for SAP HANA
RHEL-SAP-APPS | 6.8      | RAW    | Linuxový agent | 6\.8 RHEL for SAP Business Applications
|             | 7.3      | LVM    | Linuxový agent | 7\.3 RHEL for SAP Business Applications

### <a name="old-naming-convention"></a>Stará konvence pojmenování
RHEL 7 řady imagí a systému RHEL 6 řady imagí použít konkrétní verze jejich skladové položky až do názvů změnu konvence je vysvětleno výše.

Číselné SKU najdete v seznamu úplnou bitovou kopii. Microsoft a Red Hat vytvoří nové SKU číselné při dodává se nové verze.

### <a name="other-available-offers-and-skus"></a>Další dostupné nabídky a skladové položky
Úplný seznam dostupných nabídek a skladových jednotek může obsahovat další Image rámec toho, jaký je uveden v tabulce výše, například `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Tyto nabídky můžou sloužit za poskytování podpory řešení konkrétních marketplace, nebo může být publikována pro verze Preview a pro účely testování. Může být změny a odebírání kdykoli bez předchozího upozornění. Nepoužívejte je, pokud byly jejich přítomnost veřejně dokumentovány společností Microsoft nebo Red Hat.

## <a name="publishing-policy"></a>Publikování zásady
Microsoft a Red Hat aktualizovat Image vydané nový dílčí verze, podle potřeby řešit konkrétní CVEs nebo změny/aktualizace občasné konfigurace. Snažíme poskytovat aktualizované Image co nejdříve – do tří pracovních dnů po vydání nebo dostupnost CVE opravy.

Aktualizujeme pouze aktuální podverzi řady danou image. S vydáním novější podverze nepřestáváme starší vedlejší verze aktualizace. Například verze RHEL 7.6 imagí RHEL 7.5 už budou aktualizovat.

>[!NOTE]
> Aktivní virtuální počítače Azure zajištěného z RHEL s průběžnými platbami bitové kopie jsou připojené k Azure RHUI a může přijímat aktualizace a opravy, jakmile jsou vydané společností Red Hat a replikují do Azure RHUI (obvykle za méně než 24 hodin po oficiálním vydání by Red Hat) . Tyto virtuální počítače nevyžadují nové publikované image pro získávání aktualizací a zákazníci mají plnou kontrolu nad tím, kdy k zahájení aktualizace.

## <a name="image-retention-policy"></a>Zásady uchovávání informací bitové kopie
Zachovat všechny dříve publikované Image je naše aktuální zásady. Vyhrazujeme si právo odebrat bitové kopie, které způsobují problémy jakéhokoli druhu. Například může být nesprávná konfigurace z důvodu následující platformy nebo aktualizace součástí odebrán. Postupujte podle imagí, které se můžou odebrat aktuální zásady Marketplace k poskytování oznámení až na 30 dní, než odebrání image.

## <a name="next-steps"></a>Další postup
* Další informace o Azure Red Hat Update Infrastructure [tady](https://aka.ms/rhui-update).
* Informace o zásadách podpory Red Hat pro všechny verze systému RHEL můžete najít na [Red Hat Enterprise Linux životního cyklu](https://access.redhat.com/support/policy/updates/errata) stránky.
