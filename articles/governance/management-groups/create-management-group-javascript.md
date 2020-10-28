---
title: 'Rychlý Start: Vytvoření skupiny pro správu pomocí JavaScriptu'
description: V tomto rychlém startu pomocí JavaScriptu vytvoříte skupinu pro správu, která slouží k uspořádání prostředků do hierarchie prostředků.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 965e4b8f81a26cab8f6e34cab3e51d6a97f5a6a6
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676229"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>Rychlý Start: Vytvoření skupiny pro správu pomocí JavaScriptu

Skupiny pro správu jsou kontejnery, které vám pomůžou spravovat přístup, zásady a dodržování předpisů v různých předplatných. Vytvořte tyto kontejnery, abyste vytvořili efektivní a efektivní hierarchii, která se dá použít s [Azure Policy](../policy/overview.md) a [řízení přístupu na základě rolí Azure](../../role-based-access-control/overview.md). Další informace o skupinách pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](overview.md).

První skupina pro správu vytvořená v adresáři může trvat až 15 minut, než se dokončí. Při nastavování služby skupiny pro správu v Azure pro váš adresář existují procesy, které se spouštějí poprvé. Po dokončení procesu obdržíte oznámení. Další informace najdete v tématu [počáteční nastavení skupin pro správu](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Předpoklady

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

- Než začnete, ujistěte se, že je nainstalovaná minimálně verze 12 [Node.js](https://nodejs.org/) .

- Libovolný uživatel Azure AD v tenantovi může vytvořit skupinu pro správu bez oprávnění k zápisu skupiny pro správu, která jsou přiřazená tomuto uživateli, pokud není povolena [ochrana hierarchie](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Tato nová skupina pro správu se stal podřízenou skupinou kořenové skupiny pro správu nebo [výchozí skupině pro správu](./how-to/protect-resource-hierarchy.md#setting---default-management-group) a autorovi je přiděleno přiřazení role "vlastník". Služba skupiny pro správu umožňuje tuto možnost, takže přiřazení rolí není nutné na kořenové úrovni. Žádní uživatelé nemají přístup ke kořenové skupině pro správu při jejím vytvoření. Aby nedocházelo k tomu, že by globální Správci služby Azure AD mohli začít používat skupiny pro správu, umožníme vytváření počátečních skupin pro správu na kořenové úrovni.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Nastavení aplikace

Aby bylo možné povolit jazyk JavaScript dotazování na Azure Resource Graph, musí být prostředí nastavené. Tato instalace funguje bez ohledu na to, kde je možné použít JavaScript, včetně [bash ve Windows 10](/windows/wsl/install-win10).

1. Vytvořte nový Node.js projekt spuštěním následujícího příkazu.

   ```bash
   npm init -y
   ```

1. Přidejte odkaz na modul yargs.

   ```bash
   npm install yargs
   ```

1. Přidejte odkaz na modul Azure Resource Graph.

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Přidejte odkaz na knihovnu ověřování Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Ověřte v _package.js_ `@azure/arm-managementgroups` verze **1.1.0** nebo novější a `@azure/ms-rest-nodeauth` verze **3.0.5** nebo vyšší.

## <a name="create-the-management-group"></a>Vytvoření skupiny pro správu

1. Vytvořte nový soubor s názvem _index.js_ a zadejte následující kód.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const managementGroups = require("@azure/arm-managementgroups");

   if (argv.groupID && argv.displayName) {
       const createMG = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new managementGroups.ManagementGroupsAPI(credentials);
          const result = await client.createOrUpdate(
             groupId: argv.groupID,
             {
                 displayName: argv.displayName
             }
          );
          console.log(result);
       };

       createMG();
   }
   ```

1. Do terminálu zadejte následující příkaz:

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   Nezapomeňte nahradit každý `<>` zástupný symbol tokenu _identifikátorem skupiny pro správu_ a _popisným názvem skupiny pro správu_ v uvedeném pořadí.

   Při pokusu o ověření skriptu se v terminálu zobrazí zpráva podobná následující zprávě:

   > Pokud se chcete přihlásit, otevřete stránku pomocí webového prohlížeče https://microsoft.com/devicelogin a zadejte kód FGB56WJUGK, který se má ověřit.

   Po ověření v prohlížeči bude skript dál běžet.

Výsledkem vytvoření skupiny pro správu je výstup do konzoly.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat nainstalované knihovny z aplikace, spusťte následující příkaz.

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili skupinu pro správu, která bude organizovat svou hierarchii prostředků. Skupina pro správu může obsahovat předplatná nebo jiné skupiny pro správu.

Pokud chcete získat další informace o skupinách pro správu a o tom, jak spravovat hierarchii prostředků, pokračujte tady:

> [!div class="nextstepaction"]
> [Správa prostředků pomocí skupin pro správu](./manage.md)