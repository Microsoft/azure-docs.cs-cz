---
title: Známé problémy – digitální vlákna Azure
description: Získejte pomoc s rozpoznáváním a zmírněním známých problémů s využitím digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 549e1808a3b449f7d29b968cde76ef29391880b3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100608"
---
# <a name="known-issues-in-azure-digital-twins"></a>Známé problémy v případě digitálních vláken Azure

Tento článek poskytuje informace o známých problémech souvisejících s digitálními podseznamy Azure.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 chyba klienta: Chybný požadavek" v Cloud Shell

Příkazy v Cloud Shell spuštěných v *https://shell.azure.com* můžou občas selhat s chybou 400 chyba klienta: Chybná žádost o adresu URL: http://localhost:50342/oauth2/token , následované úplným trasováním zásobníku.

U digitálních vláken Azure to platí konkrétně pro následující skupiny příkazů:
* `az dt route`
* `az dt model`
* `az dt twin`

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

To se dá vyřešit tak, že znovu spustíte `az login` příkaz v Cloud Shell a dokončíte další kroky přihlášení. Za tímto účelem byste měli být schopni spustit příkaz znovu.

Případně můžete otevřít podokno Cloud Shell v Azure Portal a dokončit Cloud Shell práci z těchto kroků:

:::image type="content" source="media/includes/portal-cloud-shell.png" alt-text="Zobrazuje se Azure Portal se zvýrazněnou ikonou Cloud Shell a Cloud Shell se zobrazuje v dolní části okna portálu." lightbox="media/includes/portal-cloud-shell.png":::

Nakonec další řešení je [instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) do vašeho počítače, abyste mohli spouštět příkazy rozhraní příkazového řádku Azure v místním prostředí. V místním rozhraní příkazového řádku se tento problém netýká.

### <a name="possible-causes"></a>Možné příčiny

Toto je výsledek známého problému v Cloud Shell: [*získání tokenu z Cloud Shell přerušovaně se nezdařilo s 400 chybou klienta: Chybný požadavek*](https://github.com/Azure/azure-cli/issues/11749).

To představuje problém s tokeny ověřování instance Azure Digital prokážed a výchozím spravovaným ověřováním založeným na [identitách](../active-directory/managed-identities-azure-resources/overview.md) Cloud Shell. Krok pro odstraňování potíží s běžícími `az login` přepínači vám při ověřování nedošlo ze spravované identity, takže se tento problém rozpíná.

To nemá vliv na příkazy v digitálních událostech Azure `az dt` ze `az dt endpoint` skupin příkazů nebo, protože používají jiný typ ověřovacího tokenu (založeného na ARM), který nemá problém s spravovaným ověřováním identity Cloud Shell.

## <a name="missing-role-assignment-after-scripted-setup"></a>Chybějící přiřazení role po skriptovém nastavení

Někteří uživatelé mohou mít problémy s částí přiřazení role v tématu [*Postupy: nastavení instance a ověřování (skriptované)*](how-to-set-up-instance-scripted.md). Skript neindikuje selhání, ale role *vlastníka dat digitálních vláken Azure* není úspěšně přiřazená uživateli. Tento problém bude mít vliv na schopnost vytvořit další prostředky po silnici.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

Pokud chcete zjistit, jestli se přiřazení role po spuštění skriptu úspěšně nastavilo, postupujte podle pokynů v části [*Ověření přiřazení role uživatele*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) v článku o nastavení. Pokud se uživatel s touto rolí nezobrazuje, tento problém se vás týká.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Chcete-li řešení vyřešit, můžete ručně nastavit přiřazení role pomocí rozhraní příkazového řádku nebo Azure Portal. 

Postupujte podle těchto pokynů:
* [Rozhraní příkazového řádku](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [bran](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Možné příčiny

Pro uživatele, kteří se přihlásili pomocí osobního [účet Microsoft (MSA)](https://account.microsoft.com/account), se ID objektu zabezpečení vašeho uživatele, které identifikuje v těchto příkazech, může lišit od přihlašovacího e-mailu vašeho uživatele. proto je obtížné skriptu zjistit a použít pro správné přiřazení role.

## <a name="issue-with-interactive-browser-authentication"></a>Problém s interaktivním ověřováním prohlížeče

Při psaní ověřovacího kódu v aplikacích digitálního vlákna Azure s využitím **1.2.0** verze v **knihovně [Azure. identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)** může docházet k problémům s metodou [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) .

Nejedná se o nejnovější verzi knihovny. Nejnovější verze je **1.2.2** .

Ovlivněná metoda se používá v následujících článcích: 
* [*Kurz: vytvoření kódu klientské aplikace*](tutorial-code.md)
* [*Postupy: psaní kódu ověřování aplikace*](how-to-authenticate-client.md)
* [*Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure*](how-to-use-apis-sdks.md)

Tento problém při pokusu o ověření v okně prohlížeče obsahuje chybovou odpověď "Azure. identity. AuthenticationFailedException". Může se stát, že se okno prohlížeče zcela nespustí nebo se zobrazí ověření uživatele úspěšně, zatímco klientská aplikace se přesto nezdaří s chybou.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Pokud chcete řešení vyřešit, aktualizujte své aplikace tak, aby používaly `Azure.Identity` verzi **1.2.2** . V této verzi knihovny by měl prohlížeč načíst a ověřit podle očekávání.

### <a name="possible-causes"></a>Možné příčiny

To se vztahuje na otevřený problém s nejnovější verzí `Azure.Identity` knihovny (verze **1.2.0** ): [*ověření při použití InteractiveBrowserCredential se nezdařilo*](https://github.com/Azure/azure-sdk-for-net/issues/13940).

Tento problém se zobrazí, pokud použijete **1.2.0** verze v aplikaci digitálního vlákna v Azure, nebo pokud knihovnu přidáte do projektu bez zadání verze (která je také výchozí pro tuto nejnovější verzi).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o zabezpečení a oprávněních u digitálních vláken Azure:
* [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md)