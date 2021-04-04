---
title: Zabezpečení hypervisoru v Azure loďstvu – zabezpečení Azure
description: Technický přehled zabezpečení hypervisoru v rámci loďstva Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 766266edd663b75b893a5883e30bb48eed7bbfdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94696111"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>Zabezpečení hypervisoru v rámci loďstva Azure

Systém hypervisoru Azure je založený na Windows Hyper-V. Systém hypervisoru umožňuje správci počítače zadat oddíly hosta, které mají oddělené adresní prostory. Samostatné adresní prostory umožňují načíst operační systém a aplikace, které pracují paralelně s operačním systémem (hostitele), který se spouští v kořenovém oddílu počítače. Hostitelský operační systém (označovaný také jako privilegovaný kořenový oddíl) má přímý přístup ke všem fyzickým zařízením a periferním zařízením v systému (řadiče úložiště, síťové adaptéry). Hostitelský operační systém umožňuje, aby oddíly hosta sdílely používání těchto fyzických zařízení tím, že vystavuje "virtuální zařízení" jednotlivým oddílům hosta. Proto operační systém, který je spuštěn v hostovaném oddílu, má přístup k virtualizovaným periferním zařízením, která jsou poskytována službami virtualizace spuštěnými v kořenovém oddílu.

Hypervisor Azure zachovává následující cíle zabezpečení:

| Cíl | Zdroj |
|--|--|
| Izolace | Zásady zabezpečení připravují žádné přenosy informací mezi virtuálními počítači. Toto omezení vyžaduje možnosti v Virtual Machine Manager (VMM) a hardwaru pro izolaci paměti, zařízení, sítě a spravovaných prostředků, jako jsou trvalá data. |
| Integrita VMM | Pro zajištění celkové integrity systému je zajištěna a udržována integrita jednotlivých komponent hypervisoru. |
| Integrita platformy | Integrita hypervisoru závisí na integritě hardwaru a softwaru, na kterém závisí. I když hypervisor nemá přímou kontrolu nad integritou platformy, Azure spoléhá na hardwarové a firmware mechanismy, jako je například čip [Cerberus](project-cerberus.md) , který chrání a detekuje základní integritu platformy. Pokud dojde k ohrožení integrity platformy, VMM a hosté se nebudou moci spouštět. |
| Omezený přístup | Funkce správy jsou vykonávány jenom autorizovanými správci připojenými přes zabezpečená připojení. Principem minimálního oprávnění je vynutilo mechanismy řízení přístupu na základě role Azure (Azure RBAC). |
| Auditování | Azure umožňuje auditovat a chránit data týkající se toho, co se stane v systému, aby bylo možné je později zkontrolovat. |

Přístup Microsoftu k posílení hypervisoru Azure a subsystému virtualizace je možné rozdělit do následujících tří kategorií.

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>Silně definované hranice zabezpečení vynucované hypervisorem

Hypervisor Azure vynutil více hranic zabezpečení mezi:

- Virtualizované oddíly hosta a privilegovaný oddíl ("hostitel")
- Více hostů
- Sám sebe a hostitele
- Sám sebe a všichni hosté

Pro hranice zabezpečení hypervisoru jsou zajištěny utajení, integrita a dostupnost. Hranice se chrání před řadou útoků, včetně nevracení informací na straně kanálu, odmítnutí služby a zvýšení úrovně oprávnění.

Hranice zabezpečení hypervisoru taky poskytuje segmentaci mezi klienty pro síťový provoz, virtuální zařízení, úložiště, výpočetní prostředky a všechny další prostředky virtuálních počítačů.

## <a name="defense-in-depth-exploit-mitigations"></a>Zmírnění rizik v důkladné obraně při zneužití

V nepravděpodobném případě, že hranice zabezpečení obsahuje bezpečnostní riziko, hypervisor Azure zahrnuje několik úrovní zmírnění rizik, včetně těchto:

- Izolace hostitelských procesů hostujících součásti mezi virtuálními počítači
- Zabezpečení založené na virtualizaci (VBS) pro zajištění integrity součástí režimu uživatele a režimu jádra z zabezpečeného světa
- Více úrovní rizik zneužití. Zmírnění rizik zahrnuje náhodné přeskupování rozložení adresního prostoru (ASLR), zabránění spuštění dat (DEP), libovolné ochrany kódu, integritu toku řízení a prevenci poškození dat.
- Automatická inicializace proměnných zásobníku na úrovni kompilátoru
- Rozhraní API jádra, která automaticky nainicializuje přidělení haldy jádra vytvořená technologií Hyper-V

Tato zmírnění jsou navržená tak, aby se zajistilo, že vývoj zneužití pro chybu zabezpečení mezi virtuálními počítači je neproveditelný.

## <a name="strong-security-assurance-processes"></a>Výkonné procesy zabezpečování zabezpečení

Plocha pro útoky související s hypervisorem zahrnuje softwarové sítě, virtuální zařízení a všechny povrchy mezi virtuálními počítači. Plocha pro útok je sledována prostřednictvím automatizované integrace sestavení, která spouští pravidelné kontroly zabezpečení.

Všechny povrchy útoku na virtuální počítače jsou modelem hrozeb, revize kódu, rozmazaný a testovaný náš červený tým pro porušení hranic zabezpečení. Společnost Microsoft má [program Bounty s chybou](https://www.microsoft.com/msrc/bounty-hyper-v) , který zaplatí ocenění relevantních ohrožení zabezpečení v oprávněných verzích produktu Microsoft Hyper-V.

> [!NOTE]
> Další informace o [silných procesech zabezpečování zabezpečení](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes) v technologii Hyper-V.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co máme k zajištění integrity a zabezpečení platformy, najdete v těchto tématech:

- [Zabezpečení firmwaru](firmware.md)
- [Zabezpečené spouštění](secure-boot.md)
- [Měřené ověření spouštění a ověřování hostitele](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Šifrování neaktivních uložených dat](encryption-atrest.md)