---
title: Resetování tokenů nasazení v Azure static Web Apps (Preview)
description: Resetování tokenů ve statické Web Apps lokalitě Azure
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: fe1edb2693993d02a705039c18b04c8d1b7b9725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101744801"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps-preview"></a>Resetování tokenů nasazení v Azure static Web Apps (Preview)

Když vytváříte nový web Azure static Web Apps, Azure vygeneruje token, který se používá k identifikaci aplikace během nasazování. Během zřizování se tento token uloží jako tajný kód v úložišti GitHubu. Tento článek vysvětluje, jak používat a spravovat tento token.

Za normálních okolností se nemusíte starat o token nasazení, ale tady jsou některé důvody, proč možná budete muset token načíst nebo resetovat.

* **Ohrožení bezpečnosti tokenů**: resetujte token, pokud je vystavený externí straně.
* **Nasazení z samostatného úložiště GitHubu**: Pokud ručně nasazujete z samostatného úložiště GitHubu, musíte v novém úložišti nastavit token nasazení.

## <a name="prerequisites"></a>Požadavky

- Existující úložiště GitHub s nakonfigurovaným statickým Web Appsm Azure.
- Pokud ho nemáte, přečtěte si téma [Vytvoření první statické aplikace](getting-started.md) .

## <a name="reset-a-deployment-token"></a>Resetování tokenu nasazení

1. Klikněte na odkaz **Spravovat token nasazení** na stránce _Přehled_ na webu Azure static Web Apps.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="Správa tokenu nasazení":::

1. Klikněte na tlačítko **resetovat token** .

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="Resetování tokenu nasazení":::

1. Po zobrazení nového tokenu v poli _token nasazení_ zkopírujte token tak, že kliknete na ikonu **Kopírovat do schránky** .


## <a name="update-a-secret-in-the-github-repository"></a>Aktualizace tajného kódu v úložišti GitHubu

Aby se zajistilo automatizované nasazení, po resetování tokenu musíte nastavit novou hodnotu v odpovídajícím úložišti GitHubu.

1. Přejděte do úložiště projektu na GitHubu a klikněte na kartu **Nastavení** .
1. Klikněte na položku nabídky **tajné klíče** . Během zřizování statické webové aplikace najdete tajný kód, který se vygeneruje _AZURE_STATIC_WEB_APPS_API_TOKEN_... v části _tajné klíče úložiště_ .

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="Výpis tajných klíčů úložiště":::

    > [!NOTE]
    > Pokud jste vytvořili web Azure static Web Apps na několika větvích tohoto úložiště, zobrazí se více _AZURE_STATIC_WEB_APPS_API_TOKEN_... tajné kódy v tomto seznamu. Vyberte správný soubor podle názvu souboru, který je uvedený v poli _Upravit pracovní postup_ na kartě _Přehled_ na stránce statického Web Apps.

1. Kliknutím na tlačítko **aktualizovat** otevřete Editor.
1. Do pole _hodnota_ **vložte hodnotu** tokenu nasazení.
1. Klikněte na možnost **aktualizovat tajný klíč**.

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="Aktualizuje se tajný klíč úložiště.":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Publikování z generátoru statických webů](publish-gatsby.md)
