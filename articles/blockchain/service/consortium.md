---
title: Konsorcium služby Azure Blockchain Service
description: Přehled toho, jak služba Azure Blockchain implementuje blockchainové sítě konsorcia.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247615"
---
# <a name="azure-blockchain-service-consortium"></a>Konsorcium služby Azure Blockchain Service

Pomocí služby Azure Blockchain Service můžete vytvořit privátní blockchainové sítě konsorcia, kde může být každá blockchainová síť omezena na konkrétní účastníky v síti. Blockchain může prohlížet a komunikovat s ním pouze účastníci blockchainové sítě privátního konsorcia. Sítě konsorcia ve službě Azure Blockchain Service mohou obsahovat dva typy rolí členských účastníků:

* **Správce** – Privilegovaní účastníci, kteří mohou přijímat akce správy konsorcia a mohou se účastnit transakcí blockchainu.

* **Uživatel** – účastníci, kteří nemohou přijmout žádnou akci správy konsorcia, ale mohou se účastnit transakcí blockchainu.

Sítě konsorcia mohou být kombinací rolí účastníků a mohou mít libovolný počet jednotlivých typů rolí. Musí existovat alespoň jeden správce.

Následující diagram znázorňuje síť konsorcia s více účastníky:

![Síťový diagram privátního konsorcia](./media/consortium/network-diagram.png)

Díky správě konsorcia ve službě Azure Blockchain Service můžete spravovat účastníky v síti konsorcia. Řízení konsorcia je založeno na konsenzuálním modelu sítě. V aktuální verzi preview služba Azure Blockchain Service poskytuje centralizovaný konsensuální model pro správu konsorcia. Každý privilegovaný účastník s rolí správy může provést akce správy konsorcia, jako je například přidání nebo odebrání účastníků ze sítě.

## <a name="roles"></a>Role

Účastníci v konsorciu mohou být jednotlivci nebo organizace a může být přiřazena role uživatele nebo role správce. V následující tabulce jsou uvedeny rozdíly na vysoké úrovni mezi dvěma rolemi:

| Akce | Role uživatele | Role Správce
|--------|:----:|:------------:|
| Vytvořit nového člena | Ano | Ano |
| Pozvat nové členy | Ne | Ano |
| Nastavení nebo změna role účastníka člena | Ne | Ano |
| Změna zobrazovat název člena | Pouze pro vlastní členy | Pouze pro vlastní členy |
| Odebrání členů | Pouze pro vlastní členy | Ano |
| Účast na transakcích s blockchainem | Ano | Ano |

### <a name="user-role"></a>Role uživatele

Uživatelé jsou účastníky konsorcia bez možností správce. Nemohou se podílet na řízení členů souvisejících s konsorciem. Uživatelé mohou změnit zobrazovaný název svého člena a mohou se odebrat z konsorcia.

### <a name="administrator"></a>Správce

Správce může spravovat členy v rámci konsorcia. Správce může pozvat členy, odebrat členy nebo aktualizovat role členů v rámci konsorcia.
V rámci konsorcia musí být vždy alespoň jeden správce. Poslední správce musí před opuštěním konsorcia určit jiného účastníka jako roli správce.

## <a name="managing-members"></a>Řízení členů

Pouze správci mohou pozvat další účastníky do konsorcia. Správci zvou účastníky pomocí jejich ID předplatného Azure.

Jakmile budou účastníci pozváni, mohou se připojit k konsorciu blockchain nasazením nového člena ve službě Azure Blockchain Service. Chcete-li zobrazit a připojit se k pozvanému konsorciu, musíte zadat stejné ID předplatného Azure, které použil v pozvánce správce sítě.

Správci mohou odebrat libovolného účastníka z konsorcia, včetně jiných správců. Členové se mohou pouze odebrat z konsorcia.

## <a name="consortium-management-smart-contract"></a>Inteligentní smlouva pro správu konsorcia

Správa konsorcia ve službě Azure Blockchain Service se provádí prostřednictvím inteligentních smluv pro správu konsorcia. Inteligentní kontrakty se automaticky nasazují do vašich uzlů, když nasadíte nového člena blockchainu.

Adresu inteligentní smlouvy pro správu kořenového konsorcia si můžete prohlédnout na webu Azure Portal. **Adresa RootContract** je v části přehledu člena blockchainu.

![Adresa RootContract](./media/consortium/rootcontract-address.png)

Můžete pracovat s inteligentní smlouvou pro správu konsorcia pomocí [modulu PowerShell](manage-consortium-powershell.md)pro správu konsorcia , portálu Azure nebo přímo prostřednictvím inteligentního kontraktu pomocí účtu Ethereum generovaného službou Azure Blockchain Service.

## <a name="ethereum-account"></a>Ethereum účet

Při vytvoření člena je vytvořen klíč účtu Ethereum. Služba Azure Blockchain používá klíč k vytváření transakcí souvisejících se správou konsorcia. Klíč účtu Ethereum spravuje služba Azure Blockchain Service automaticky.

Účet člena si můžete prohlédnout na webu Azure Portal. Členský účet je v sekci přehledu členů blockchainu.

![Členský účet](./media/consortium/member-account.png)

Svůj účet Ethereum můžete obnovit kliknutím na členský účet a zadáním nového hesla. Adresa účtu Ethereum i heslo budou resetovány.  

## <a name="next-steps"></a>Další kroky

Akce správy konsorcia lze přistupovat prostřednictvím prostředí PowerShell. Další informace najdete [v tématu Správa členů konsorcia ve službě Azure Blockchain Service pomocí PowerShellu](manage-consortium-powershell.md).
