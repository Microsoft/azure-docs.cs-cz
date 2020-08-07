---
title: Co je služba Azure Static Web Apps?
description: Klíčové funkce a funkce služby Azure static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 47140bccc8c2a1b69f083502d031a9db6d21294e
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835778"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Co je Azure static Web Apps Preview?

Azure static Web Apps je služba, která automaticky sestaví a nasadí do Azure všechny služby Stack Web Apps z úložiště GitHubu.

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Přehled statického Web Apps":::

Pracovní postup Azure static Web Apps je přizpůsobený dennímu pracovnímu postupu vývojáře. Aplikace jsou sestavené a nasazené na základě interakcí GitHubu.

Když vytvoříte prostředek Azure Static Web Apps, Azure nastaví v úložišti zdrojového kódu této aplikace pracovní postup GitHub Actions, který bude monitorovat vámi vybranou větev. Pokaždé, když zadáte potvrzení změn nebo přijmete žádosti o přijetí změn do sledované větve, akce GitHub automaticky vytvoří a nasadí vaši aplikaci a její rozhraní API do Azure.

Statické webové aplikace se běžně vytvářejí pomocí knihoven a platforem, jako jsou Angular, React, Svelte nebo Vue. Tyto aplikace zahrnují prostředky HTML, CSS, JavaScript a grafické materiály. U tradičního webového serveru se tyto prostředky obsluhují z jednoho serveru spolu se všemi požadovanými koncovými body rozhraní API.

Statické Web Apps jsou statické prostředky oddělené od tradičního webového serveru a místo toho se obsluhují z míst geograficky rozmístěných po celém světě. Tato distribuce zajišťuje mnohem rychlejší poskytování souborů, protože soubory jsou fyzicky blíže ke koncovým uživatelům. Kromě toho jsou koncové body rozhraní API hostovány pomocí [architektury bez serveru](../azure-functions/functions-overview.md), což předchází nutnosti úplného back-end serveru dohromady.

## <a name="key-features"></a>Klíčové funkce

- **Webové hostování** pro statický obsah, jako jsou HTML, CSS, JavaScript a obrázky.
- **Integrovaná podpora rozhraní API** poskytovaná Azure Functions.
- **Integrace GitHubu první strany** , kde změny úložiště spustí sestavení a nasazení.
- **Globálně distribuovaný** statický obsah, který zapisuje obsah blíž k uživatelům.
- **Bezplatné certifikáty SSL**, které se automaticky Obnovují.
- **Vlastní domény** \* pro zajištění vlastního přizpůsobení aplikace.
- **Bezproblémový model zabezpečení** s reverzním proxy serverem při volání rozhraní API, který nevyžaduje žádnou konfiguraci CORS
- **Integrace zprostředkovatele ověřování** pomocí Azure Active Directory, Facebooku, Google, GitHubu a Twitteru.
- **Přizpůsobitelná definice role autorizace** a přiřazení.
- **Pravidla směrování back-endu** , která umožňují plnou kontrolu nad obsahem a trasami, které poskytujete.
- **Vygenerovaly se přípravné verze** , které využívají žádosti o přijetí změn, které povolují verzi Preview webu před publikováním.

## <a name="what-you-can-do-with-static-web-apps"></a>Co se dá dělat se statickými Web Apps

- **Vytvářejte moderní aplikace JavaScriptu** pomocí architektur a knihoven, jako je například [úhlová](getting-started.md#tabpanel_CeZOj-G++Q_angular), [reakce](getting-started.md#tabpanel_CeZOj-G++Q_react), [Svelte](https://docs.microsoft.com/learn/modules/publish-app-service-static-web-app-api/), [Vue](getting-started.md#tabpanel_CeZOj-G++Q_vue) s back-endu [Azure Functions](apis.md) .
- **Publikování statických webů** pomocí platforem, jako je [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md).
- **Nasaďte webové aplikace** s rozhraními, jako jsou [Next.js](deploy-nextjs.md) a [Nuxt.js](deploy-nuxtjs.md).

\*Registrace ve vrcholu domény nejsou ve verzi Preview podporované.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Sestavení první statické aplikace](getting-started.md)
