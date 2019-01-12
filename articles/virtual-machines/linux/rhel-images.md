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
ms.date: 12/18/2018
ms.author: borisb
ms.openlocfilehash: dbef04710e1a91cfda8b039605b517de9524ee19
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229570"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Image Red Hat Enterprise Linux v Azure
Tento článek popisuje dostupných imagí Red Hat Enterprise Linux (RHEL) na webu Azure Marketplace spolu s zásady po jejich názvy a uchování.

Informace o zásadách podpory Red Hat pro všechny verze systému RHEL můžete najít na [Red Hat Enterprise Linux životního cyklu](https://access.redhat.com/support/policy/updates/errata) stránky.

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
Všechny aktuálně publikovaných imagí RHEL použít model průběžných plateb a jsou připojené k [Red Hat Update Infrastructure (RHUI) v Azure](https://aka.ms/rhui-update). Z důvodu omezení RHUI byla přijata novým zásadám vytváření názvů pro Image RHEL 7 řady. V tuto chvíli nebylo změněno řady systému RHEL 6 pojmenování.

Omezení je ve skutečnosti, že bez – selektivní `yum update` spustí pro virtuální počítač připojený k RHUI, verze RHEL se aktualizuje na nejnovější verzi aktuální řady. Další informace najdete v tématu [tento odkaz](https://aka.ms/rhui-udate). Výsledkem může nejasnosti při zřízené image RHEL 7.2 RHEL 7.6 se stane po aktualizaci. Stále můžete zřídit ze starší image podle pokynů ve výše uvedených příkladech tak, že explicitně zadáte na požadovanou verzi. Pokud požadovaná verze není zadán při zřizování nové image RHEL 7, se zřídí nejnovější image.

>[!NOTE]
> RHEL for SAP sadu bitových kopií zůstane pevné verzi RHEL. V důsledku toho jejich zásady vytváření názvů obsahuje konkrétní verzi ve skladové Položce.

>[!NOTE]
> RHEL 6 sadu bitových kopií nepřesunula k novým zásadám vytváření názvů.

Tyto nabídky jsou že skladové položky jsou aktuálně k dispozici pro obecné použití:
Nabídka| Skladová jednotka (SKU) | Dělení | Zřizování | Poznámky
:----|:----|:-------------|:-------------|:-----
RHEL | NEZPRACOVANÁ 7 | NEZPRACOVANÉ | Linuxový agent | RHEL 7 řady imagí
| | 7 LVM | LVM | Linuxový agent | RHEL 7 řady imagí
| | 7-RAW-CI | NEZPRACOVANÁ CI | Cloud-init | RHEL 7 řady imagí
| | 6.7 | NEZPRACOVANÉ | Linuxový agent | Image RHEL 6.7, staré pojmenování
| | 6.8 | NEZPRACOVANÉ | Linuxový agent | Jak je uvedeno výše pro RHEL 6.8 stejný
| | 6.9 | NEZPRACOVANÉ | Linuxový agent | Jak je uvedeno výše pro RHEL 6.9 stejný
| | 6.10 | NEZPRACOVANÉ | Linuxový agent | Jak je uvedeno výše pro RHEL 6.10 stejný
| | 7.2 | NEZPRACOVANÉ | Linuxový agent | Stejný, jak je uvedeno výše pro RHEL 7.2
| | 7.3 | NEZPRACOVANÉ | Linuxový agent | Jak je uvedeno výše pro RHEL 7.3 stejný
| | 7.4 | NEZPRACOVANÉ | Linuxový agent | Jak je uvedeno výše pro RHEL 7.4 stejný
| | 7.5 | NEZPRACOVANÉ | Linuxový agent | Stejný, jak je uvedeno výše pro RHEL 7.5
RHEL SAP | 7.4 | LVM | Linuxový agent | 7.4 RHEL for SAP HANA a obchodní aplikace
| | 7.5 | LVM | Linuxový agent | 7.5 RHEL for SAP HANA a obchodní aplikace
RHEL. SAP HANA | 6.7 | NEZPRACOVANÉ | Linuxový agent | RHEL 6.7 pro SAP HANA
| | 7.2 | LVM | Linuxový agent | RHEL 7.2 pro SAP HANA
| | 7.3 | LVM | Linuxový agent | 7.3 RHEL for SAP HANA
RHEL. SAP APLIKACE | 6.8 | NEZPRACOVANÉ | Linuxový agent | 6.8 RHEL for SAP Business Applications
| | 7.3 | LVM | Linuxový agent | 7.3 RHEL for SAP Business Applications

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