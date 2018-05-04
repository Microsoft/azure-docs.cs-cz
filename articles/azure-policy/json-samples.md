---
title: Ukázky šablony zásad | Microsoft Docs
description: Ukázky JSON pro zásad Azure
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: na
ms.topic: samples
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 01/17/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 3473cb5260773fda0534c4f0aca1db731cce74eb
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="templates-for-azure-policy"></a>Šablony pro Azure zásad

Následující tabulka obsahuje odkazy na šablony json Azure zásady. Tyto ukázky jsou součástí [úložiště zásad Azure ukázky](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Compute**||
| [Schválené Image virtuálních počítačů](scripts/allowed-custom-images.md) | Vyžaduje, aby jen schválení vlastní Image jsou nasazené ve vašem prostředí. Můžete určit pole schválené Image ID. |
| [Audit, když virtuální počítač nepoužívá spravované disku](scripts/create-vm-managed-disk.md) | Audity při vytváření virtuálního počítače, který nepoužívá spravované disky.|
| [Audit Pokud rozšíření neexistuje.](scripts/audit-ext-not-exist.md) | Audity Pokud rozšíření není nasazena s virtuálním počítačem. Určete rozšíření vydavatele a typ, který chcete zkontrolovat, zda byl nasazen. |
| [Povolit vlastní image virtuálního počítače ze skupiny prostředků](scripts/allow-custom-vm-image.md) |  Vyžaduje se, že vlastní Image pocházejí z skupiny schválené prostředků. Zadáte název skupiny prostředků schválené. |
| [Výhody použití hybridní Odepřít](scripts/deny-hybrid-use.md) | Zakáže použití služby Azure hybridní použití zvýhodnění (AHUB). Použijte, pokud nechcete povolit použití místního licencí. |
| [Není povolené rozšíření virtuálního počítače](scripts/not-allowed-vm-ext.md) | Zakáže použití zadané rozšíření. Můžete určit pole obsahující typy zakázané rozšíření. |
| [Povolit jenom určité platformy image virtuálního počítače](scripts/allow-certain-vm-image.md) | Vyžaduje, aby virtuální počítače použít konkrétní verzi UbuntuServer. |
| [Vytvoření virtuálního počítače pomocí spravované disku](scripts/use-managed-disk-vm.md) | Vyžaduje, že virtuální počítače používat spravované disky.|
|**Monitorování**||
| [Nastavení diagnostiky auditu](scripts/audit-diag-setting.md) | Audity případného nastavení diagnostiky není povolen pro typy prostředků. Můžete určit pole typů prostředků, zkontrolujte, zda je povoleno nastavení pro diagnostiku. |
|**Název a text konvence**||
| [Povolit více vzory názvů](scripts/allow-multiple-name-patterns.md) | Povolit jednu z mnoha vzory názvů, který se má použít pro prostředky. |
| [Vyžadovat jako vzor](scripts/enforce-like-pattern.md) | Zajistěte, aby názvy prostředků splňují například podmínku pro vzor. |
| [Vyžadovat vzor pro shodu](scripts/enforce-match-pattern.md) | Zajistěte, aby názvy prostředků shodují se vzorem názvů. |
| [Vyžadovat vzor pro shodu značky](scripts/enforce-tag-match-pattern.md) | Zkontrolujte, zda hodnota značky odpovídá vzorku textu. |
|**Síť**||
| [Povolená SKU brány aplikace](scripts/allowed-app-gate-sku.md) | Vyžaduje, aby používal application Gateway schválené SKU. Zadáte pole schválené SKU. |
| [Audit Pokud sledovací proces sítě není povolena pro oblast](scripts/net-watch-not-enabled.md) | Audity, pokud není u zadané oblasti povolena sledovací proces sítě. Zadáte název oblasti, zkontrolujte, zda je povoleno sledovací proces sítě. |
| [Skupina NSG X na každý síťový adaptér](scripts/nsg-on-nic.md) | Vyžaduje, že skupina zabezpečení sítě konkrétní se používá s každé rozhraní virtuální sítě. Zadáte ID skupiny zabezpečení sítě používat. |
| [Skupina NSG X v každé podsíti](scripts/nsg-on-subnet.md) | Vyžaduje se, zda skupina zabezpečení sítě konkrétní používá pro všechny virtuální podsítě. Zadáte ID skupiny zabezpečení sítě používat. |
| [Povolené Express Route šířky pásma](scripts/allowed-er-band.md) | Vyžaduje, aby používal express trasy zadanou sadu šířek pásma. Můžete zadat řadu skladových položek, které lze zadat pro Express Route. |
| [Povolené umístění partnerského vztahu Expressroute](scripts/allowed-peering-er.md) | Vyžaduje použití Express trasy umístění partnerského vztahu. Zadáte pole povolených umístění partnerského vztahu. |
| [Povolené Express Route SKU](scripts/allowed-er-skus.md) | Vyžaduje, aby používal Express trasy schválené SKU. Zadáte pole povolených SKU. |
| [Povolená SKU služby Vyrovnávání zatížení](scripts/allowed-lb-skus.md) | Vyžaduje, aby používal nástroje pro vyrovnávání zatížení schválené SKU. Zadáte pole povolených SKU. |
| [Žádná síť partnerský vztah k síti ER](scripts/no-peering-er-net.md) | Zakáže síť partnerského vztahu z bylo možné přidružit k síti v určené skupině prostředků. Používejte k ochraně připojení s centrální spravované síťové infrastruktury. Zadáte název skupiny prostředků, aby se zabránilo přidružení. |
| [Žádná uživatelská tabulka definovanou trasou](scripts/no-user-def-route-table.md)  |Zakazuje virtuálních sítí nasazuje s tabulkou trasy definované uživatelem. |
| [Povolená SKU brány virtuální sítě](scripts/allowed-vn-gate-sku.md) | Vyžaduje, aby používal brány virtuální sítě schválené typů SKU a brány. Určete pole schválené SKU a pole typy schválené brány. |
| [Použijte schválené podsíť pro síťová rozhraní virtuálních počítačů](scripts/use-approved-subnet-vm-nics.md) | Vyžaduje, aby používal síťových rozhraní schválené podsítě. Zadáte ID schválené podsítě. |
| [Použít schválené vNet pro síťová rozhraní virtuálních počítačů](scripts/use-approved-vnet-vm-nics.md) | Vyžaduje, aby síťových rozhraní použít schválené virtuální síť. Zadáte ID schválené virtuální sítě. |
|**Značky**||
| [Fakturace značky zásad Initiative](scripts/billing-tags-policy-init.md) | Vyžaduje, aby hodnoty stanovené značky pro náklady center a název produktu. S využitím předdefinovaných zásad používá a vynucuje požadované značky. Pro značky zadáte požadované hodnoty.  |
| [Vynutit značku a její hodnotu na skupiny prostředků](scripts/enforce-tag-rg.md) | Vyžaduje značky a hodnoty pro skupinu prostředků. Zadáte název požadované značky a hodnotu.  |
|**SQL**||
| [Auditování SQL DB úroveň nastavení kontroly](scripts/audit-sql-db-audit-setting.md) | Audity nastavení auditování databáze SQL, pokud tato nastavení se neshodují zadané nastavení. Zadáte hodnotu, která určuje, zda nastavení auditu by měl být povoleno nebo zakázáno.  |
| [Stav šifrování transparentní data auditu](scripts/audit-trans-data-enc-status.md) | Audity SQL databáze transparentní šifrování dat, není-li povoleno.  |
| [Nastavení detekce hrozeb úrovně DB kontroly](scripts/audit-db-threat-det-setting.md) | Audity výstrahy zásady zabezpečení databáze SQL, pokud tyto zásady nejsou nastaveny do zadaného stavu. Zadáte hodnotu, která určuje, zda je povoleno detekce hrozeb.  |
| [Audit úroveň systému SQL Server Audit nastavení](scripts/audit-sql-ser-leve-audit-setting.md) | Nastavení serveru SQL server audit audity, pokud tato nastavení se neshodují zadané nastavení. Zadáte hodnotu, která určuje, zda nastavení auditu by měl být povoleno nebo zakázáno. |
| [Audit nastavení detekce hrozeb úrovni serveru](scripts/audit-sql-ser-threat-det-setting.md) | Audity výstrahy zásady zabezpečení databáze SQL, pokud tyto zásady nejsou nastaveny do zadaného stavu. Zadáte hodnotu, která určuje, zda je povoleno detekce hrozeb.  |
| [Audit žádný správce Azure Active Directory](scripts/audit-no-aad-admin.md) | Audit, když není žádný správce Azure Active Directory přiřazené k systému SQL server. |
| [Povolená SKU SQL DB](scripts/allowed-sql-db-skus.md) | Vyžaduje databází SQL používat schválené SKU. Zadáte pole povolené identifikátory SKU nebo polí názvů SKU povolené. |
|**Úložiště**||
| [Povolená SKU pro účty úložiště a virtuální počítače](scripts/allowed-skus-storage.md) | Vyžaduje, že účty úložiště a virtuální počítače používat schválené SKU. Předdefinované zásady používá k zajištění schválené SKU. Určete pole schválené virtuálních počítačů SKU a pole účet schválené úložiště SKU. |
| [Ujistěte se, provoz https pouze pro účet úložiště](scripts/ensure-https-stor-acct.md) | Vyžaduje účty úložiště použít provoz HTTPS.  |
| [Odepřít nástrojů přístup vrstvení pro účty úložiště](scripts/deny-cool-access-tiering.md) | Zakáže použití nástrojů přístup vrstvení pro účty úložiště blob.  |
| [Ujistěte se, šifrování souborů úložiště](scripts/ensure-store-file-enc.md) | Vyžaduje že šifrování souborů je povoleno pro účty úložiště.  |
|**Předdefinované zásady**||
| [Povolených umístění](scripts/allowed-locs.md) | Vyžaduje, aby všechny prostředky jsou nasazené do schválených umístění. Zadáte pole schválené umístění.  |
| [Typy povolené prostředků](scripts/allowed-res-types.md) | Zajišťuje, jsou nasazeny pouze typy schválené prostředků. Můžete určit pole typů prostředků, které jsou povoleny.  |
| [Povolené účet úložiště SKU](scripts/allowed-stor-acct-skus.md) | Vyžaduje, aby používaly účty úložiště schválené SKU. Zadáte pole schválené SKU. |
| [Použít značky a jeho výchozí hodnota](scripts/apply-tag-def-val.md) | Připojí zadaný název značky a hodnotu, pokud není k dispozici tuto značku. Zadáte název značky a použít hodnotu.  |
| [Audit šifrování databáze SQL](scripts/sql-database-encryption-audit.md) | Audity, pokud databáze SQL neobsahuje transparentní šifrování dat povolené. |
| [Nastavení auditování systému SQL Server audit](scripts/sql-server-audit.md) | Audity založené na tom, jestli jsou povolené toto nastavení auditu systému SQL server. |
| [Vynucení šifrování Data Lake Store](scripts/enforce-datalakestore-encryption.md) | Odmítne účty Data Lake Store, které nemají povolené šifrování. |
| [Vynutit značku a její hodnota](scripts/enforce-tag-val.md) | Vyžaduje zadaný název značky a hodnotu. Zadáte název značky a hodnotu vynutit.  |
| [Není povoleno typy prostředků](scripts/not-allowed-res-type.md) | Zakáže nasazení typů zadaný prostředek. Můžete určit pole typů prostředků blokování.  |
| [Vyžaduje systém SQL Server verze 12.0](scripts/req-sql-12.md) | Vyžaduje použití verze 12.0 serverech SQL.  |
| [Vyžadovat šifrování účtu úložiště](scripts/req-store-acct-enc.md) | Vyžaduje šifrování úložiště účtu použití objektů blob.  |
