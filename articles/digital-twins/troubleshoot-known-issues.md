---
title: Známé problémy – digitální vlákna Azure
description: Získejte pomoc s rozpoznáváním a zmírněním známých problémů s využitím digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 118b869cb97a7dd3ce65566a994a27d2a65a7402
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184930"
---
# <a name="known-issues-in-azure-digital-twins"></a>Známé problémy v případě digitálních vláken Azure

Tento článek poskytuje informace o známých problémech souvisejících s digitálními podseznamy Azure.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 chyba klienta: Chybný požadavek" v Cloud Shell

**Popis problému:** Příkazy v Cloud Shell spuštěných v *https://shell.azure.com* můžou občas selhat s chybou 400 chyba klienta: Chybná žádost o adresu URL: http://localhost:50342/oauth2/token , následované úplným trasováním zásobníku.

| Týká se to mi? | Příčina | Řešení |
| --- | --- | --- |
| V &nbsp; &nbsp; rámci digitálních &nbsp; vláken Azure to ovlivní následující skupiny příkazů:<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | Toto je výsledek známého problému v Cloud Shell: [*získání tokenu z Cloud Shell přerušovaně se nezdařilo s 400 chybou klienta: Chybný požadavek*](https://github.com/Azure/azure-cli/issues/11749).<br><br>To představuje problém s tokeny ověřování instance Azure Digital prokážed a výchozím spravovaným ověřováním založeným na [identitách](../active-directory/managed-identities-azure-resources/overview.md) Cloud Shell. <br><br>To nemá vliv na příkazy v digitálních událostech Azure `az dt` ze `az dt endpoint` skupin příkazů nebo, protože používají jiný typ ověřovacího tokenu (na základě Azure Resource Manager), který nemá problém s ověřováním spravované identity Cloud Shell. | Jedním ze způsobů, jak tento problém vyřešit, je spustit `az login` příkaz v Cloud Shell a dokončit následné přihlašovací kroky. Tím dojde k přepnutí vaší relace ze spravovaného ověřování identity. tím se vyhnete potížím s kořenovým adresářem. Za tímto účelem byste měli být schopni spustit příkaz znovu.<br><br>Případně můžete otevřít podokno Cloud Shell v Azure Portal a dokončit Cloud Shell práci.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Obrázek ikony Cloud Shell na panelu ikon Azure Portal" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>Nakonec další řešení je [instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) do vašeho počítače, abyste mohli spouštět příkazy rozhraní příkazového řádku Azure v místním prostředí. V místním rozhraní příkazového řádku se tento problém netýká. |


## <a name="missing-role-assignment-after-scripted-setup"></a>Chybějící přiřazení role po skriptovém nastavení

**Popis problému:** Někteří uživatelé mohou mít problémy s částí přiřazení role v tématu [*Postupy: nastavení instance a ověřování (skriptované)*](how-to-set-up-instance-scripted.md). Skript neindikuje selhání, ale role *vlastníka dat digitálních vláken Azure* není úspěšně přiřazená uživateli. Tento problém bude mít vliv na schopnost vytvořit další prostředky po silnici.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

| Týká se to mi? | Příčina | Řešení |
| --- | --- | --- |
| Pokud chcete zjistit, jestli se přiřazení role po spuštění skriptu úspěšně nastavilo, postupujte podle pokynů v části [*Ověření přiřazení role uživatele*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) v článku o nastavení. Pokud se uživatel s touto rolí nezobrazuje, tento problém se vás týká. | Pro uživatele, kteří se přihlásili pomocí osobního [účet Microsoft (MSA)](https://account.microsoft.com/account), se ID objektu zabezpečení vašeho uživatele, které identifikuje v těchto příkazech, může lišit od přihlašovacího e-mailu vašeho uživatele. proto je obtížné skriptu zjistit a použít pro správné přiřazení role. | Chcete-li řešení vyřešit, můžete ručně nastavit přiřazení role pomocí [instrukcí CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions) nebo [Azure Portal instrukcí](how-to-set-up-instance-portal.md#set-up-user-access-permissions). |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Problémy s ověřováním pomocí interaktivního prohlížeče v Azure. identity 1.2.0

**Popis problému:** Při psaní ověřovacího kódu v aplikacích digitálního vlákna Azure s využitím **1.2.0** verze v **knihovně [Azure. identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)** může docházet k problémům s metodou [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) . Tato možnost představuje při pokusu o ověření v okně prohlížeče jako odpověď na chybu "Azure. identity. AuthenticationFailedException". Může se stát, že se okno prohlížeče zcela nespustí nebo se zobrazí ověření uživatele úspěšně, zatímco klientská aplikace se přesto nezdaří s chybou.

| Týká se to mi? | Příčina | Řešení |
| --- | --- | --- |
| &nbsp;Ovlivněná &nbsp; Metoda &nbsp; se &nbsp; používá &nbsp; v &nbsp; &nbsp; následujících článcích:<br><br>[*Kurz: vytvoření kódu klientské aplikace*](tutorial-code.md)<br><br>[*Postupy: psaní kódu ověřování aplikace*](how-to-authenticate-client.md)<br><br>[*Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure*](how-to-use-apis-sdks.md) | Někteří uživatelé mají tento problém s verzí **1.2.0** `Azure.Identity` knihovny. | Pokud chcete řešení vyřešit, aktualizujte své aplikace tak, aby používaly [novější verzi](https://www.nuget.org/packages/Azure.Identity) nástroje `Azure.Identity` . Po aktualizaci verze knihovny by měl prohlížeč načíst a ověřit podle očekávání. |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Problém s výchozím ověřováním přihlašovacích údajů Azure v Azure. identity 1.3.0

**Popis problému:** Při psaní ověřovacího kódu pomocí **1.3.0** verze v **knihovně [Azure. identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)** mají někteří uživatelé problémy s metodou [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet?view=azure-dotnet&preserve-view=true) použitou v mnoha ukázkách v těchto dokumentech digitálních vláken Azure. To představuje chybovou odpověď "Azure. identity. AuthenticationFailedException: ověřování SharedTokenCacheCredential se nezdařilo", když se kód pokusí ověřit.

| Týká se to mi? | Příčina | Řešení |
| --- | --- | --- |
| `DefaultAzureCredential` používá se ve většině příkladů dokumentace pro tuto službu, která zahrnuje ověřování. Pokud píšete ověřovací kód pomocí `DefaultAzureCredential` verze 1.3.0 `Azure.Identity` knihovny a vidíte tuto chybovou zprávu, bude to mít vliv na vás. | To je nejspíš důsledek některých potíží s konfigurací v nástroji `Azure.Identity` . | Jedna strategie, kterou je třeba vyřešit, je vyloučit `SharedTokenCacheCredential` z přihlašovacích údajů, jak je popsáno v tomto [DefaultAzureCredential problému](https://github.com/Azure/azure-sdk/issues/1970) , ve kterém je aktuálně otevřená `Azure.Identity` .<br>Další možností je změnit aplikaci tak, aby používala starší verzi nástroje `Azure.Identity` , jako je například [verze 1.2.3](https://www.nuget.org/packages/Azure.Identity/1.2.3). Tato funkce nemá žádný vliv na funkce digitálních vláken Azure a proto je také přijatým řešením. |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o zabezpečení a oprávněních u digitálních vláken Azure:
* [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md)