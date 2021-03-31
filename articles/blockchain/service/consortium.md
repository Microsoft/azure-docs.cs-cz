---
title: Konsorcium služeb Azure blockchain
description: Přehled toho, jak služba Azure blockchain implementuje sítě blockchain pro konsorcium.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84712526"
---
# <a name="azure-blockchain-service-consortium"></a>Konsorcium služeb Azure blockchain

Pomocí služby Azure blockchain můžete vytvořit soukromé sítě konsorcia blockchain, kde je možné každou blockchain síť omezit na konkrétní účastníky v síti. Blockchain mohou zobrazit a pracovat s pouze účastníci v blockchain síti privátního sdružení. Sítě konsorcia ve službě Azure blockchain můžou obsahovat dva typy rolí členu účastníka:

* Účastníci s oprávněním **správce** , kteří můžou přebírat akce správy konsorcia a můžou se zúčastnit transakcí blockchain.

*  Účastníci, kteří nemohou provádět žádnou akci správy konsorcia, ale mohou se zúčastnit transakcí blockchain.

Sítě konsorcia můžou být kombinací rolí účastníka a můžou mít libovolný počet jednotlivých typů rolí. Musí existovat alespoň jeden správce.

Následující diagram znázorňuje síť konsorcia s více účastníky:

![Síťový diagram privátního konsorcia](./media/consortium/network-diagram.png)

Se správou konsorcia ve službě Azure blockchain můžete spravovat účastníky v síti konsorcia. Správa konsorcia je založena na modelu konsensu v síti. V aktuální verzi Preview poskytuje služba Azure blockchain centralizovaný model konsensu pro správu konsorcia. Každý privilegovaný účastník s rolí spravovat může provádět akce správy konsorcia, například přidávání nebo odebírání účastníků ze sítě.

## <a name="roles"></a>Role

Účastníci konsorcia můžou být jednotlivci nebo organizace a je možné jim přiřadit roli uživatele nebo roli správce. V následující tabulce jsou uvedeny rozdíly vysoké úrovně mezi dvěma rolemi:

| Akce | Role uživatele | Role Správce
|--------|:----:|:------------:|
| Vytvořit nového člena | Ano | Ano |
| Pozvat nové členy | Ne | Ano |
| Nastavení nebo změna role člena účastníka | Ne | Ano |
| Zobrazovaný název člena změny | Pouze pro vlastního člena | Pouze pro vlastního člena |
| Odebrání členů | Pouze pro vlastního člena | Ano |
| Účast v transakcích blockchain | Ano | Ano |

### <a name="user-role"></a>Role uživatele

Uživatelé jsou účastníky konsorcia bez schopností správce. Nemůžou se podílet na správě členů spojených s konsorciem. Uživatelé mohou změnit zobrazovaný název členů a mohou se z konsorcia odebrat sami.

### <a name="administrator"></a>Správce

Správce může spravovat členy v rámci konsorcia. Správce může pozvat členy, odebrat členy nebo aktualizovat role členů v rámci konsorcia.
V rámci konsorcia musí být vždy alespoň jeden správce. Poslední správce musí před opustili konsorcium zadat jiného účastníka jako roli správce.

## <a name="managing-members"></a>Správa členů

Pouze správci mohou pozvat další účastníky do konsorcia. Správci můžou pozvat účastníky pomocí svého ID předplatného Azure.

Po pozvání se můžou účastníci připojit k blockchain Consortium nasazením nového člena ve službě Azure blockchain. Chcete-li zobrazit pozvaní konsorcium, je nutné zadat stejné ID předplatného Azure, které bylo použito v pozvání správce sítě.

Správci mohou odebrat jakéhokoli účastníka z konsorcia, včetně jiných správců. Členové mohou z konsorcia odebrat pouze sebe sama.

## <a name="consortium-management-smart-contract"></a>Inteligentní kontrakt správy konsorcia

Správa konsorcia ve službě Azure blockchain se provádí prostřednictvím inteligentních kontraktů správy konsorcia. Inteligentní kontrakty se při nasazení nového člena blockchain automaticky nasadí do vašich uzlů.

Adresa inteligentního kontraktu správy root Consortium se dá zobrazit v Azure Portal. **Adresa RootContract** je v oddílu přehled členů blockchain.

![Adresa RootContract](./media/consortium/rootcontract-address.png)

Pomocí [modulu PowerShell](manage-consortium-powershell.md)pro správu konsorcia, Azure Portal nebo přímo prostřednictvím inteligentního kontraktu s využitím účtu ethereem generovaného službou Azure blockchain, můžete komunikovat s inteligentní smlouvou správy konsorcia.

## <a name="ethereum-account"></a>Účet ethereem

Při vytvoření člena se vytvoří klíč účtu Ethereem. Služba Azure blockchain používá klíč k vytváření transakcí souvisejících se správou konsorcia. Klíč účtu Ethereem se automaticky spravuje službou Azure blockchain.

Členský účet lze zobrazit v Azure Portal. Členský účet je v oddílu přehled členů blockchain.

![Členský účet](./media/consortium/member-account.png)

Svůj účet Ethereem můžete resetovat kliknutím na svůj členský účet a zadáním nového hesla. Bude resetována jak adresa účtu Ethereem, tak heslo.  

## <a name="next-steps"></a>Další kroky

Akce správy konsorcia je k dispozici prostřednictvím PowerShellu. Další informace najdete v tématu [Správa členů konsorcia ve službě Azure blockchain pomocí prostředí PowerShell](manage-consortium-powershell.md).
