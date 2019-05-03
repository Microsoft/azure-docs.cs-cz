---
title: Azure Blockchain Service Consortium
description: ''
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e745a4ee4789ef46a61b5cb0bbf806c41ef631ec
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027912"
---
# <a name="azure-blockchain-service-consortium"></a>Azure Blockchain Service Consortium

Pomocí služby Azure Blockchain, můžete vytvořit privátní consortium blockchain sítí, kde každá síť blockchain lze omezit na konkrétní účastníky v síti. Pouze účastníky v síti privátní consortium blockchain můžete zobrazit a pracovat s blockchainem. Sítě konsorcia ve službě Azure Blockchain Service může obsahovat dva typy rolí účastníka člena:

* **Správce** -privilegovaného účastníky, kteří mohou provádět akce správy consortium a účastnit se blockchain transakce.

* **Uživatel** -účastníky, kteří nelze provádět žádnou akci správu consortium, ale mohou být součástí transakce blockchain.

Consortium sítě může být kombinací účastníka role a může mít libovolný počet každého typu role. Musí existovat alespoň jeden správce.

Následující diagram znázorňuje sítě konsorcia několika účastníci:

![Síťový diagram soukromé W3C](./media/consortium/network-diagram.png)

Pomocí consortium správy ve službě Azure Blockchain můžete spravovat účastníky v síti consortium. Správa konsorcia je založena na modelu caiq sítě. V aktuální verzi preview poskytuje služba Azure Blockchain modelu centralizované shody pro správu consortium. Každý účastník privilegovaných s rolí správce může trvat consortium akce správy, jako je například přidávání nebo odebírání účastníky v síti.

## <a name="roles"></a>Role

Účastníci konsorcium může být jednotlivcům i organizacím a je možné přiřadit roli uživatele nebo roli správce. Následující tabulka uvádí základní rozdíly mezi dvě role:

| Akce | Role uživatele | Role správce
|--------|:----:|:------------:|
| Vytvoření nového člena | Ano | Ano |
| Pozvat nové členy | Ne | Ano |
| Nastavení nebo změna účastníka roli člena | Ne | Ano |
| Změna členů zobrazovaný název | Pouze pro vlastní člena | Pouze pro vlastní člena |
| Odebrat členy | Pouze pro vlastní člena | Ano |
| Účasti v transakcích blockchain | Ano | Ano |

### <a name="user-role"></a>Role uživatele

Uživatelé jsou consortium účastníci se žádná funkce správce. Se nemůže podílet na správu členy související s konsorcia. Uživatele můžete změnit jejich zobrazovaného jména člena a sami odebrat z konsorcium.

### <a name="administrator"></a>Správce

Správce může spravovat členy v rámci konsorcia. Správce může zvát členy, odebírat nebo aktualizovat členy rolí v rámci konsorcia.
Vždy musí být v rámci konsorcia alespoň jeden správce. Poslední správce musíte zadat jiným účastníkem jako roli správce před opuštěním konsorcium.

## <a name="managing-members"></a>Správa členů

Pouze správci může pozvat ostatní účastníky na konsorcia. Správci pozvat účastníky pomocí jejich ID předplatného Azure.

Jakmile pozváni, účastníky můžete připojit blockchain consortium nasazením nového člena ve službě Azure Blockchain. Zobrazit a připojit se k pozvaný consortium, musíte zadat stejné ID předplatného Azure v pozvánce používaná správcem sítě.

Každý účastník, mohou správci odebrat ze consortium, včetně jiných správců. Členy můžete pouze odebrat sami z konsorcium.

## <a name="consortium-management-smart-contract"></a>Inteligentní smlouvy Consortium správy

Consortium správy ve službě Azure Blockchain se provádí prostřednictvím consortium správu chytrých kontraktů. Když nasadíte nový člen blockchain se chytrých kontraktů automaticky nasadí do uzly.

Adresa kontraktu inteligentní správy consortium kořenové lze zobrazit na webu Azure Portal. **RootContract adresu** je v oddílu přehled blockchain člena.

![Adresa RootContract](./media/consortium/rootcontract-address.png)

Můžete pracovat s kontraktem consortium inteligentní správy pomocí rozhraní pro správu consortium [modulu PowerShell](manage-consortium-powershell.md)Azure portálu, nebo přímo prostřednictvím inteligentního kontraktu pomocí služby Azure Blockchain generované ethereum během účet.

## <a name="ethereum-account"></a>Etherea účtu

Když se člen, se vytvoří klíče účtu služby Etherea. Služba Azure Blockchain používá klíč k vytváření transakcí související se správou consortium. Klíč účtu ethereum během je spravované službou Azure Blockchain automaticky.

Členský účet můžete zobrazit na webu Azure Portal. V části Přehled blockchain člen je členský účet.

![Členský účet](./media/consortium/member-account.png)

Váš účet Etherea můžete obnovit kliknutím na váš členský účet a zadat nové heslo. Adresa Etherea účtu a heslo bude resetováno.  

## <a name="next-steps"></a>Další postup

[Jak spravovat členy ve službě Azure Blockchain pomocí Powershellu](manage-consortium-powershell.md)
