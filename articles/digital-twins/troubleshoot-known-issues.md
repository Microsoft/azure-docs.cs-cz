---
title: Známé problémy – digitální vlákna Azure
description: Získejte pomoc s rozpoznáváním a zmírněním známých problémů s využitím digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8fce451d9b806d2fa9a4f3d9e1c117de0aaa9fc0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531311"
---
# <a name="known-issues-in-azure-digital-twins"></a>Známé problémy v případě digitálních vláken Azure

Tento článek poskytuje informace o známých problémech souvisejících s digitálními podseznamy Azure.

## <a name="managing-event-routes-in-the-azure-portal"></a>Správa tras událostí v Azure Portal

Pokud jste se k portálu přihlásili pomocí osobního [**účet Microsoft (MSA)**](https://account.microsoft.com/account/Account), jako je například *@outlook.com* účet, zobrazí se vám při pokusu o správu Směrování událostí na portálu obrazovka, *která vám bude vyžadovat oprávnění k zobrazení tras událostí* , a to bez ohledu na úroveň oprávnění.

:::image type="content" source="media/troubleshoot-known-issues/event-route-need-permission.png" alt-text="Snímek obrazovky z Azure Portal chyby oprávnění při pokusu o vytvoření směrování událostí v instanci digitálních vláken Azure":::

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Uživatelé, kteří v tuto chvíli nemůžou spravovat trasy událostí na portálu, můžou pořád spravovat trasy událostí pomocí rozhraní API nebo rozhraní příkazového řádku Azure pro digitální vlákna. Přechod na jeden z těchto nástrojů pro správu Směrování událostí je doporučovanou strategií pro zmírnění tohoto problému.

Pokyny k této možnosti najdete v tématu [*Postupy: Správa koncových bodů a tras*](how-to-manage-routes.md).

### <a name="possible-causes"></a>Možné příčiny

K portálu jste se přihlásili pomocí osobního [účet Microsoft (MSA)](https://account.microsoft.com/account/Account), jako je třeba *@outlook.com* účet. Správa tras událostí v Azure Portal je v tuto chvíli dostupná jenom pro uživatele Azure na účtech podnikových domén.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 chyba klienta: Chybný požadavek" v Cloud Shell

Příkazy v Cloud Shell můžou selhat neúspěšné s chybou 400 klienta chyba: Chybný požadavek na adresu URL: http://localhost:50342/oauth2/token "následovaný úplným trasováním zásobníku.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Tuto možnost můžete vyřešit opakovaným spuštěním `az login` příkazu a dokončením následujících kroků přihlášení.

Potom byste měli být schopni příkaz znovu spustit.

### <a name="possible-causes"></a>Možné příčiny

Toto je výsledek známého problému v Cloud Shell: [*získání tokenu z Cloud Shell přerušovaně se nezdařilo s 400 chybou klienta: Chybný požadavek*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o zabezpečení a oprávněních u digitálních vláken Azure:
* [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md)