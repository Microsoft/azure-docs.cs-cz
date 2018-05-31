---
title: Ukázkové šablony zásad
description: Ukázky JSON pro Azure Policy
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/17/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 4b9096c1fb0d9ee74849e259a6e0af2486c5d29b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195120"
---
# <a name="templates-for-azure-policy"></a>Šablony pro Azure Policy

Následující tabulka obsahuje odkazy na šablony json pro Azure Policy. Tyto ukázky jsou součástí [úložiště ukázek služby Azure Policy](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Compute**||
| [Schválené image virtuálních počítačů](scripts/allowed-custom-images.md) | Vyžaduje, aby se ve vašem prostředí nasazovaly jenom schválené vlastní image. Zadejte pole ID schválených imagí. |
| [Provedení auditu virtuálního počítače, který nepoužívá spravovaný disk](scripts/create-vm-managed-disk.md) | Provede audit, pokud se vytvoří virtuální počítač, který nepoužívá spravované disky.|
| [Provedení auditu, když neexistuje rozšíření](scripts/audit-ext-not-exist.md) | Provede audit, pokud není s virtuálním počítačem nasazené rozšíření. Zadáte vydavatele a typ rozšíření, u kterého chcete zkontrolovat, jestli je nasazené. |
| [Povolení vlastní image virtuálního počítače ze skupiny prostředků](scripts/allow-custom-vm-image.md) |  Vyžaduje, aby vlastní image pocházely ze schválené skupiny prostředků. Zadejte název schválené skupiny prostředků. |
| [Odepření zvýhodněného hybridního využití](scripts/deny-hybrid-use.md) | Zakáže používat Zvýhodněné hybridní využití Azure (AHUB). To využijete v případech, že nechcete povolit, aby se používaly místní licence. |
| [Nepovolená rozšíření virtuálního počítače](scripts/not-allowed-vm-ext.md) | Zakáže použití zadaných rozšíření. Zadáte pole obsahující typy zakázaných rozšíření. |
| [Povolení jenom určité image platformy virtuálního počítače](scripts/allow-certain-vm-image.md) | Vyžaduje, aby virtuální počítače používaly konkrétní verzi UbuntuServeru. |
| [Vytvoření virtuálního počítače, který používá spravovaný disk](scripts/use-managed-disk-vm.md) | Vyžaduje, aby virtuální počítače používaly spravované disky.|
|**Monitorování**||
| [Auditování nastavení diagnostiky](scripts/audit-diag-setting.md) | Audituje, jestli pro zadané typy prostředků nejsou povolená nastavení diagnostiky. Zadáte pole typů prostředků, u kterých se zkontroluje, jestli jsou povolená nastavení diagnostiky. |
|**Zásady vytváření názvů a textů**||
| [Povolení více vzorů pro názvy](scripts/allow-multiple-name-patterns.md) | Povolíte jeden z mnoha vzorů názvů, který se použije pro prostředky. |
| [Vyžadování vzoru s podmínkou like](scripts/enforce-like-pattern.md) | Zajistí, aby názvy prostředků splňovaly podmínku like pro vzor. |
| [Vyžadování shody se vzorem](scripts/enforce-match-pattern.md) | Zajistí, aby názvy prostředků odpovídaly vzoru pro názvy. |
| [Vyžadování shody značky se vzorem](scripts/enforce-tag-match-pattern.md) | Zajistí, aby hodnota značky odpovídala textovému vzoru. |
|**Síť**||
| [Povolené skladové položky Application Gateway](scripts/allowed-app-gate-sku.md) | Vyžaduje, aby aplikační brány používaly schválenou skladovou položku. Zadejte pole schválených skladových položek. |
| [Provedení auditu, pokud oblast nemá povolenou službu Network Watcher](scripts/net-watch-not-enabled.md) | Provede audit, pokud pro zadanou oblast není povolený sledovací proces sítě. Zadejte název oblasti, pro kterou se zkontroluje, jestli je povolený sledovací proces sítě. |
| [NSG X ve všech síťových rozhraních](scripts/nsg-on-nic.md) | Vyžaduje, aby se ve všech virtuálních síťových rozhraních používala konkrétní skupina zabezpečení sítě. Zadejte ID skupiny zabezpečení sítě, která se má použít. |
| [NSG X ve všech podsítích](scripts/nsg-on-subnet.md) | Vyžaduje, aby se ve všech virtuálních podsítích používala konkrétní skupina zabezpečení sítě. Zadejte ID skupiny zabezpečení sítě, která se má použít. |
| [Povolená šířka pásma pro ExpressRoute](scripts/allowed-er-band.md) | Vyžaduje, aby připojení ExpressRoute používala zadaná rozmezí šířky pásma. Zadejte pole skladových položek, které je možné zadat pro ExpressRoute. |
| [Povolené umístění partnerského vztahu pro ExpressRoute](scripts/allowed-peering-er.md) | Vyžaduje, aby připojení ExpressRoute používala určená umístění partnerského vztahu. Zadáte pole povolených umístění partnerského vztahu. |
| [Povolené skladové položky ExpressRoute](scripts/allowed-er-skus.md) | Vyžaduje, aby připojení ExpressRoute používala schválenou skladovou položku. Zadejte pole povolených skladových položek. |
| [Povolené skladové položky nástroje pro vyrovnávání zatížení](scripts/allowed-lb-skus.md) | Vyžaduje, aby nástroje pro vyrovnávání zatížení používaly schválenou skladovou položku. Zadejte pole povolených skladových položek. |
| [Blokování partnerských vztahů se sítí ER](scripts/no-peering-er-net.md) | Zakáže přidružení partnerských vztahů k síti v zadané skupině prostředků. Tuto možnost využijete, pokud chcete zabránit připojení k centrálně spravované síťové infrastruktuře. Zadejte název skupiny prostředků, aby se zabránilo přidružení. |
| [Žádná tabulka směrování definovaná uživatelem](scripts/no-user-def-route-table.md)  |Zakáže nasazení virtuálních sítí s tabulkou směrování definovanou uživatelem. |
| [Povolené skladové položky brány virtuální sítě](scripts/allowed-vn-gate-sku.md) | Vyžaduje, aby brány virtuálních sítí používaly schválený typ skladové položky a brány. Zadejte pole schválených skladových položek a pole schválených typů bran. |
| [Použití schválené podsítě pro síťová rozhraní virtuálních počítačů](scripts/use-approved-subnet-vm-nics.md) | Vyžaduje, aby síťová rozhraní používala schválenou podsíť. Zadejte ID schválené podsítě. |
| [Použití schválené virtuální sítě pro síťová rozhraní virtuálních počítačů](scripts/use-approved-vnet-vm-nics.md) | Vyžaduje, aby síťová rozhraní používala schválenou virtuální síť. Zadáte ID schválené virtuální sítě. |
|**Značky**||
| [Iniciativa zásad fakturačních značek](scripts/billing-tags-policy-init.md) | Vyžaduje zadání hodnot značek pro nákladové středisko a název produktu. S využitím předdefinovaných zásad používá a vynucuje požadované značky. Pro značky zadáte požadované hodnoty.  |
| [Vynucení značky a její hodnoty ve skupinách prostředků](scripts/enforce-tag-rg.md) | Vyžaduje značku a hodnotu u skupiny prostředků. Zadejte požadovaný název a hodnotu značky.  |
|**SQL**||
| [Audit nastavení auditování na úrovni databáze SQL](scripts/audit-sql-db-audit-setting.md) | Provede audit nastavení auditování databáze SQL v případě, že tato nastavení nevyhovují zadaným kritériím. Zadejte hodnotu, která určí, jestli má být nastavení auditování povoleno nebo ne.  |
| [Auditování stavu transparentního šifrování dat](scripts/audit-trans-data-enc-status.md) | Provede audit transparentního šifrování dat databáze SQL, jestliže není povoleno.  |
| [Auditování nastavení detekce hrozeb na úrovni databáze](scripts/audit-db-threat-det-setting.md) | Provede audit zásad výstrah zabezpečení databáze SQL, pokud tyto zásady nejsou nastavené na zadaný stav. Zadejte hodnotu, která určí, jestli je detekce hrozeb povolená nebo ne.  |
| [Audit nastavení auditování na úrovni SQL Serveru](scripts/audit-sql-ser-leve-audit-setting.md) | Provede audit nastavení auditování SQL Serveru v případě, že tato nastavení nevyhovují zadaným kritériím. Zadejte hodnotu, která určí, jestli má být nastavení auditování povoleno nebo ne. |
| [Audit nastavení detekce hrozeb na úrovni serveru](scripts/audit-sql-ser-threat-det-setting.md) | Provede audit zásad výstrah zabezpečení databáze SQL, pokud tyto zásady nejsou nastavené na zadaný stav. Zadejte hodnotu, která určí, jestli je detekce hrozeb povolená nebo ne.  |
| [Provedení auditu, když není žádný správce Azure Active Directory](scripts/audit-no-aad-admin.md) | Provedete audit v situaci, kdy k SQL serveru není přiřazený žádný správce Azure Active Directory. |
| [Povolené skladové položky pro databáze SQL](scripts/allowed-sql-db-skus.md) | Vyžaduje, aby databáze SQL používaly schválenou skladovou položku. Zadáte pole ID povolených skladových položek nebo pole názvů povolených skladových položek. |
|**Úložiště**||
| [Povolené skladové položky účtů úložišť a virtuálních počítačů](scripts/allowed-skus-storage.md) | Vyžaduje, aby účty úložišť a virtuální počítače používaly schválené skladové položky. K ověření schválených skladových položek slouží integrované zásady. Pole schválených skladových položek virtuálních počítačů a pole schválených skladových položek účtů úložišť zadáváte vy. |
| [Zajištění výhradně provozu HTTPS pro účet úložiště](scripts/ensure-https-stor-acct.md) | Vyžaduje, aby účty úložiště používaly provoz HTTPS.  |
| [Odepření studených úrovní přístupu pro účty úložiště](scripts/deny-cool-access-tiering.md) | Zakáže použití studených úrovní přístupu pro účty úložiště objektů blob.  |
| [Zajištění šifrování souborů úložiště](scripts/ensure-store-file-enc.md) | Vyžaduje, aby bylo pro účty úložiště povolené šifrování souborů.  |
|**Předdefinovaná zásada**||
| [Povolená umístění](scripts/allowed-locs.md) | Vyžaduje, aby všechny prostředky byly nasazené do schválených umístění. Zadáte pole schválených umístění.  |
| [Povolené typy prostředků](scripts/allowed-res-types.md) | Zajišťuje, že se nasadí jenom schválené typy prostředků. Zadáte pole typů prostředků, které jsou povoleny.  |
| [Povolené skladové položky účtu úložiště](scripts/allowed-stor-acct-skus.md) | Vyžaduje, aby účty úložiště používaly schválenou skladovou položku. Zadejte pole schválených skladových položek. |
| [Použití značky a její výchozí hodnoty](scripts/apply-tag-def-val.md) | Připojí název a hodnotu zadané značky v případě, že se tato značka neposkytne. Zadáte název a hodnotu značky, které se mají použít.  |
| [Auditování šifrování databáze SQL](scripts/sql-database-encryption-audit.md) | Provede audit, jestli databáze SQL nemá povolené transparentní šifrování dat. |
| [Auditování nastavení auditování serveru SQL](scripts/sql-server-audit.md) | Provede audit serveru SQL podle toho, jestli jsou povolená nastavení auditování. |
| [Vynucení šifrování Data Lake Store](scripts/enforce-datalakestore-encryption.md) | Odmítne účty Data Lake Store, které nemají povolené šifrování. |
| [Vynucení použití značky a její hodnoty](scripts/enforce-tag-val.md) | Vyžaduje určený název a hodnotu značky. Zadáte název a hodnotu značky, které se mají vynutit.  |
| [Nepovolené typy prostředků](scripts/not-allowed-res-type.md) | Zakáže nasazení zadaných typů prostředků. Zadejte pole typů prostředků, které chcete blokovat.  |
| [Vyžadovat SQL Server verze 12.0](scripts/req-sql-12.md) | Vyžaduje, aby SQL Servery používaly verzi 12.0.  |
| [Vyžadovat šifrování účtu úložiště](scripts/req-store-acct-enc.md) | Vyžaduje, aby účet úložiště používal šifrování objektů blob.  |
