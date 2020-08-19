---
title: Známé problémy – digitální vlákna Azure
description: Získejte pomoc s rozpoznáváním a zmírněním známých problémů s využitím digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 9130a3248e881c9d4e2c9bfe9017f43198d50f51
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590162"
---
# <a name="known-issues-in-azure-digital-twins"></a>Známé problémy v případě digitálních vláken Azure

Tento článek poskytuje informace o známých problémech souvisejících s digitálními podseznamy Azure.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 chyba klienta: Chybný požadavek" v Cloud Shell

Příkazy v Cloud Shell můžou selhat neúspěšné s chybou 400 klienta chyba: Chybný požadavek na adresu URL: http://localhost:50342/oauth2/token "následovaný úplným trasováním zásobníku.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Tuto možnost můžete vyřešit opakovaným spuštěním `az login` příkazu a dokončením následujících kroků přihlášení.

Potom byste měli být schopni příkaz znovu spustit.

### <a name="possible-causes"></a>Možné příčiny

Toto je výsledek známého problému v Cloud Shell: [*získání tokenu z Cloud Shell přerušovaně se nezdařilo s 400 chybou klienta: Chybný požadavek*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="missing-role-assignment-after-scripted-setup"></a>Chybějící přiřazení role po skriptovém nastavení

Někteří uživatelé mohou mít problémy s částí přiřazení role v tématu [*Postupy: nastavení instance a ověřování (skriptované)*](how-to-set-up-instance-scripted.md). Skript neindikuje, že se nejedná o selhání, ale role *vlastníka digitálních vláken Azure (Preview)* není úspěšně přiřazená uživateli, a to bude mít vliv na schopnost vytvářet další prostředky po silnici.

Pokud chcete zjistit, jestli se přiřazení role po spuštění skriptu úspěšně nastavilo, postupujte podle pokynů v části [*Ověření přiřazení role uživatele*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) v článku o nastavení. Pokud se uživatel s touto rolí nezobrazuje, tento problém se vás týká.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Chcete-li řešení vyřešit, můžete ručně nastavit přiřazení role pomocí rozhraní příkazového řádku nebo Azure Portal. 

Postupujte podle těchto pokynů:
* [Rozhraní příkazového řádku](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [bran](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Možné příčiny

Pro uživatele, kteří se přihlásili pomocí osobního [účet Microsoft (MSA)](https://account.microsoft.com/account), se ID objektu zabezpečení vašeho uživatele, které identifikuje v těchto příkazech, může lišit od přihlašovacího e-mailu uživatele, což ztěžuje tomu, aby skript mohl zjistit a použít pro správné přiřazení role.

## <a name="issue-with-interactive-browser-authentication"></a>Problém s interaktivním ověřováním prohlížeče

Při psaní ověřovacího kódu v aplikacích digitálního vlákna Azure pomocí nejnovější verze (verze **1.2.0**) **knihovny [Azure. identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) **může docházet k problémům s metodou [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) .

Ovlivněná metoda se používá v následujících článcích: 
* [*Kurz: vytvoření kódu klientské aplikace*](tutorial-code.md)
* [*Postupy: psaní kódu ověřování aplikace*](how-to-authenticate-client.md)
* [*Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure*](how-to-use-apis-sdks.md)

Tento problém při pokusu o ověření v okně prohlížeče obsahuje chybovou odpověď "Azure. identity. AuthenticationFailedException". Může se stát, že se okno prohlížeče zcela nespustí nebo se zobrazí ověření uživatele úspěšně, zatímco klientská aplikace se přesto nezdaří s chybou.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Chcete-li řešení vyřešit, aplikace explicitně používají Azure. identity verze **1.1.1**. V této verzi knihovny by měl prohlížeč načíst a ověřit podle očekávání.

>[!NOTE]
> Nestačí přidat knihovnu bez zadání jakékoli verze, protože to bude i nadále výchozí pro nejnovější **1.2.0**. Je nutné explicitně zadat verzi **1.1.1** .

### <a name="possible-causes"></a>Možné příčiny

Jedná se o nekompatibilitu mezi digitálními Vlákenami Azure a nejnovější verzí knihovny Azure. identity **1.2.0**verze. 

Tento problém se zobrazí, pokud ve své aplikaci používáte **1.2.0** verze, nebo pokud knihovnu přidáte do projektu bez zadání verze (jako je také výchozí pro tuto nejnovější verzi).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o zabezpečení a oprávněních u digitálních vláken Azure:
* [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md)