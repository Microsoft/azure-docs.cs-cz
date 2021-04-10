---
title: Nastavení místního vývoje pro statickou Web Apps Azure
description: Naučte se nastavit místní vývojové prostředí pro statickou Web Apps Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 04/02/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 8a45d490d060febc18d77c8487c9f562fd2a914a
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275492"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Nastavení místního vývoje pro Azure static Web Apps Preview

Při publikování do cloudu má web Azure static Web Apps mnoho služeb, které společně fungují, jako by se jedná o stejnou aplikaci. Mezi tyto služby patří:

- Statická webová aplikace
- Rozhraní API pro Azure Functions
- Služby ověřování a autorizace
- Služby Směrování a konfigurace

Tyto služby musí vzájemně komunikovat a služba Azure static Web Apps zpracovává tuto integraci pro vás v cloudu.

V místním prostředí se ale tyto služby neseskupují automaticky.

Aby se zajistilo podobné prostředí jako v Azure, rozhraní příkazového [řádku Azure Static Web Apps](https://github.com/Azure/static-web-apps-cli) poskytuje tyto služby:

- Místní statický server lokality
- Server proxy k vývojovému serveru front-end rozhraní
- Proxy k koncovým bodům rozhraní API – k dispozici prostřednictvím Azure Functions Core Tools
- Maketa ověřování a autorizační Server
- Vynucování místních tras a nastavení konfigurace

## <a name="how-it-works"></a>Jak to funguje

Následující tabulka ukazuje, jak se požadavky zpracovávají místně.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Tok požadavků a odpovědí v Azure static Web App CLI":::

> [!IMPORTANT]
> Přejděte na adresu [http://localhost:4280](http://localhost:4280) pro přístup k aplikaci obsluhované rozhraním příkazového řádku.

- **Požadavky odeslané** na port `4280` se předávají příslušnému serveru v závislosti na typu požadavku.

- Požadavky na **statický obsah** , jako je HTML nebo CSS, se zpracovávají interním serverem pro statický obsah CLI nebo serverem front-end rozhraní pro ladění.

- Žádosti o **ověření a autorizaci** zpracovává emulátor, který pro vaši aplikaci poskytuje falešný profil identity.

- **Functions Core Tools runtime** zpracovává požadavky na rozhraní API webu.

- **Odpovědi** ze všech služeb jsou vraceny do prohlížeče, jako kdyby byly všechny jednotlivé aplikace.

## <a name="prerequisites"></a>Požadavky

- **Stávající web Azure Static Web Apps**: Pokud ho ještě nemáte, začněte s aplikací [Vanilla-API](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate) Starter.
- **[Node.js](https://nodejs.org) s npm**: Spusťte [Node.js verzi LTS](https://nodejs.org) , která zahrnuje přístup k [npm](https://www.npmjs.com/).
- **[Visual Studio Code](https://code.visualstudio.com/)**: používá se pro ladění aplikace API, ale nevyžaduje se pro rozhraní příkazového řádku.

## <a name="get-started"></a>Začínáme

Otevřete terminál do kořenové složky stávajícího webu Azure static Web Apps.

1. Nainstalujte rozhraní příkazového řádku.

    `npm install -g @azure/static-web-apps-cli`

1. Sestavte aplikaci, pokud to vyžaduje vaše aplikace.

    Spustit `npm run build` nebo ekvivalentní příkaz pro váš projekt.

1. Přejděte do výstupního adresáře aplikace. Výstupní složky se často nazývají _Build_ nebo něco podobného.

1. Spusťte rozhraní příkazového řádku.

    `swa start`

1. Přejděte k [http://localhost:4280](http://localhost:4280) zobrazení aplikace v prohlížeči.

### <a name="other-ways-to-start-the-cli"></a>Další způsoby, jak spustit rozhraní příkazového řádku

| Description | Příkaz |
|--- | --- |
| Obsluha konkrétní složky | `swa start ./output-folder` |
| Použití běžícího vývojového serveru architektury | `swa start http://localhost:3000` |
| Spuštění aplikace Functions ve složce | `swa start ./output-folder --api ./api` |
| Použití spuštěné aplikace Functions | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Emulace autorizace a ověřování

Statický Web Apps CLI emuluje [tok zabezpečení](./authentication-authorization.md) implementovaný v Azure. Když se uživatel přihlásí, můžete v aplikaci definovat falešný profil identity, který jste vrátili.

Když se například pokusíte přejít na `/.auth/login/github` , vrátí se stránka, která vám umožní definovat profil identity.

> [!NOTE]
> Emulátor funguje s jakýmkoli poskytovatelem zabezpečení, nikoli jenom GitHubem.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Místní ověřování a emulátor autorizace":::

Emulátor poskytuje stránku, která vám umožní zadat následující hlavní hodnoty [klienta](./user-information.md#client-principal-data) :

| Hodnota | Popis |
| --- | --- |
| **Uživatelské jméno** | Název účtu, který je přidružený k poskytovateli zabezpečení. Tato hodnota se zobrazí jako `userDetails` vlastnost v objektu zabezpečení klienta a je generována automaticky, pokud nezadáte hodnotu. |
| **ID uživatele** | Hodnota, kterou vygenerovalo rozhraní příkazového řádku  |
| **Role** | Seznam názvů rolí, kde jsou jednotlivé názvy na novém řádku  |

Po přihlášení:

- `/.auth/me`K načtení [objektu zabezpečení klienta](./user-information.md)uživatele můžete použít koncový bod nebo koncový bod funkce.

- Když přejdete k `./auth/logout` vymazání objektu zabezpečení klienta a odhlásíte uživatele s přípravou.

## <a name="debugging"></a>Ladění

Ve statické webové aplikaci existují dva kontexty ladění. První je pro web se statickým obsahem a druhý pro funkce rozhraní API. Místní ladění je možné tím, že umožňuje, aby statické Web Apps CLI používaly pro jeden nebo oba tyto kontexty vývojové servery.

Následující kroky ukazují běžný scénář, který používá vývojové servery pro kontexty ladění.

1. Spusťte server pro vývoj statických webů. Tento příkaz je specifický pro rozhraní front-end, které používáte, ale často se dodává ve formě příkazů `npm run build` , jako jsou, `npm start` nebo `npm run dev` .

1. Otevřete složku aplikace API v Visual Studio Code a spusťte ladicí relaci.

1. Předejte adresy pro statický Server a Server rozhraní API do příkazu tak, že je uvedete `swa start` v pořadí.

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

Následující snímky obrazovky znázorňují terminály pro typický scénář ladění:

Web statického obsahu je spuštěn prostřednictvím `npm run dev` .

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Server pro vývoj statických webů":::

Aplikace Azure Functions API spouští ladicí relaci v Visual Studio Code.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Ladění rozhraní API Visual Studio Code":::

Statický Web Apps CLI se spouští pomocí vývojových serverů.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Terminál Azure static Web Apps CLI":::

Nyní požadavky na přechod přes port `4280` se směrují buď na server pro vývoj statických obsahu, nebo na relaci ladění rozhraní API.

Další informace o různých scénářích ladění s pokyny k přizpůsobení portů a adres serverů najdete v části [úložiště CLI statického Web Apps Azure](https://github.com/Azure/static-web-apps-cli).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace aplikace](configuration.md)
