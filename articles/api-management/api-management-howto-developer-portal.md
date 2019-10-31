---
title: Přehled portálu pro vývojáře Azure API Management – Azure API Management | Microsoft Docs
description: Seznamte se s portálem pro vývojáře v API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: eb5e2c2e2eeb0f29eb74b3727ecf14d70d2381f6
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176694"
---
# <a name="azure-api-management-developer-portal-overview"></a>Přehled portálu pro vývojáře Azure API Management

Portál pro vývojáře je automaticky generovaný plně přizpůsobitelný web s dokumentací vašich rozhraní API. Je tam, kde můžou příjemci rozhraní API zjišťovat vaše rozhraní API, učit se, jak je používat, žádat o přístup a vyzkoušet si je.

Tento článek popisuje rozdíly mezi místně hostovanými a spravovanými verzemi portálu pro vývojáře v API Management. Vysvětluje také její architekturu a poskytuje odpovědi na nejčastější dotazy.

> [!IMPORTANT]
> [Naučte se migrovat z verze Preview na všeobecně dostupnou verzi portálu pro](#preview-to-ga) vývojáře.

![Portál pro vývojáře API Management](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>Spravované a samostatně hostované verze

Portál pro vývojáře můžete vytvořit dvěma způsoby:

- **Spravovaná verze** – úpravou a přizpůsobením portálu, který je součástí vaší instance API Management a je přístupný prostřednictvím `<your-api-management-instance-name>.developer.azure-api.net`adresy URL. Informace o přístupu a přizpůsobení spravovaného portálu najdete v [tomto článku v dokumentaci](api-management-howto-developer-portal-customize.md) .
- **Samoobslužná verze** – nasazením a vlastním hostováním portálu mimo instanci API Management. Tento přístup umožňuje upravit základ kódu na portálu a zvětšit poskytované základní funkce. Také je potřeba upgradovat portál na nejnovější verzi sami. Podrobnosti a pokyny najdete v [úložišti GitHub se zdrojovým kódem portálu][1]. [Kurz pro spravovanou verzi](api-management-howto-developer-portal-customize.md) se provede pomocí panelu pro správu portálu, který je taky vybraný v samoobslužné verzi.

## <a name="portal-architectural-concepts"></a>Koncepce architektury portálu

Komponenty portálu mohou být logicky rozděleny do dvou kategorií: *kód* a *obsah*.

*Kód* je udržován v [úložišti GitHub][1] a zahrnuje:

- Widgety, které reprezentují vizuální prvky a kombinují HTML, JavaScript, styl možností, nastavení a mapování obsahu. Příkladem je obrázek, textový odstavec, formulář, seznam rozhraní API atd.
- Definice stylů – určení způsobu, jakým mohou být widgety ve stylu
- Modul, který generuje statické webové stránky z obsahu portálu a je napsán v JavaScriptu
- Vizuální Editor – umožňuje přizpůsobení v prohlížeči a možnosti vytváření obsahu.

*Obsah* je rozdělen do dvou podkategorií: *obsah portálu* a *API Management obsah*.

*Obsah portálu* je specifický pro portál a zahrnuje:

- Stránky – například úvodní stránka, kurzy k rozhraní API, příspěvky na blogu
- Média – obrázky, animace a další obsah založený na souborech
- Rozložení – šablony, které jsou porovnány s adresou URL a definují způsob zobrazení stránek
- Styly – hodnoty pro definice stylu, například písma, barvy, ohraničení
- Nastavení – konfigurace, například favicon, metadata webu

*Obsah portálu*, s výjimkou médií, se vyjadřuje jako dokumenty JSON.

*API Management obsah* zahrnuje entity, jako jsou rozhraní API, operace, produkty a odběry.

Portál je založen na upraveném rozvětvení [architektury Paperbits](https://paperbits.io/). Původní funkce Paperbits se rozšířily tak, aby poskytovaly widgety specifické pro API Management (například seznam rozhraní API, seznam produktů) a konektor pro API Management službu pro ukládání a načítání obsahu.

## <a name="faq"></a>Nejčastější dotazy

V této části odpovíme na běžné otázky týkající se nového portálu pro vývojáře, který má obecnou povahu. Otázky specifické pro samostatnou verzi najdete [v části wiki v úložišti GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> způsob migrace z verze Preview portálu?

Pomocí verze Preview portálu pro vývojáře jste v rámci služby API Management zřídili obsah verze Preview. Výchozí obsah byl významně upraven v všeobecně dostupné verzi pro lepší uživatelské prostředí. Obsahuje také nové pomůcky.

Pokud používáte spravovanou verzi, obnovte obsah portálu kliknutím na **obnovit obsah** v části nabídka **operace** . Potvrzením této operace dojde k odebrání veškerého obsahu portálu a zřízení nového výchozího obsahu. V API Management službě se automaticky aktualizoval modul portálu.

![Resetování obsahu portálu](media/api-management-howto-developer-portal/reset-content.png)

Pokud používáte samoobslužnou verzi, pomocí `scripts/cleanup.bat` a `scripts/generate.bat` z úložiště GitHubu odeberte existující obsah a zřiďte nový obsah. Ujistěte se, že upgradujete kód vašeho portálu na nejnovější verzi z úložiště GitHub předem.

Pokud nechcete obnovit obsah portálu, můžete zvážit použití nově dostupných pomůcek na všech stránkách. Stávající pomůcky se automaticky aktualizovaly na nejnovější verze.

Pokud byl portál zřízen po oznámení obecné dostupnosti, měl by již být součástí nového výchozího obsahu. Z vaší strany se nevyžaduje žádná akce.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>Jak se dá migrovat z původního portálu pro vývojáře na nový portál pro vývojáře?

Portály nejsou kompatibilní a je potřeba migrovat obsah ručně.

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Má nový portál všechny funkce starého portálu?

Nový portál pro vývojáře nepodporuje *aplikace* a *problémy*. Pokud jste ve starém portálu použili *problémy* a potřebujete je v novém, pošlete komentář ve [vyhrazeném problému GitHubu](https://github.com/Azure/api-management-developer-portal/issues/122).

### <a name="has-the-old-portal-been-deprecated"></a>Byl starý portál zastaralý?

Starý portál pro vývojáře a vydavatele teď představují *starší* funkce – budou dostávat jenom aktualizace zabezpečení. Nové funkce se implementují jenom na novém portálu pro vývojáře.

Vyřazení starší verze portálů bude oznámeno samostatně. Pokud máte dotazy, obavy nebo komentáře, vyvolejte je [ve vyhrazeném problému GitHubu](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="how-can-i-automate-portal-deployments"></a>Jak můžu automatizovat nasazení portálu?

Můžete programově přistupovat k obsahu portálu pro vývojáře a spravovat ho prostřednictvím REST API bez ohledu na to, jestli používáte spravovanou nebo místně hostovanou verzi.

Rozhraní API je popsané v [části wiki úložiště GitHubu][2]. Dá se taky použít k automatizaci migrace obsahu portálu mezi prostředími – například z testovacího prostředí do produkčního prostředí. Další informace o tomto procesu najdete [v tomto článku v dokumentaci](https://aka.ms/apimdocs/migrateportal) na GitHubu.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Podporuje portál Azure Resource Manager šablony nebo je kompatibilní se sadou API Management DevOps Resource Kit?

Ne.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Musím pro závislosti spravovaného portálu povolit další připojení VNET?

Ne.

### <a name="im-getting-a-cors-error-when-using-the-interactive-console-what-should-i-do"></a>Při použití interaktivní konzoly se mi zobrazuje chyba CORS Co mám dělat?

Interaktivní konzola vytvoří požadavek rozhraní API na straně klienta z prohlížeče. Problém CORS můžete vyřešit tak, že do svých rozhraní API přidáte [zásadu CORS](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) . Můžete buď zadat všechny parametry ručně (například počátek jako https://contoso.com) nebo použít zástupnou hodnotu `*`.

## <a name="next-steps"></a>Další kroky

Další informace o novém portálu pro vývojáře:

- [Přístup k portálu spravovaného vývojáře a jeho přizpůsobení](api-management-howto-developer-portal-customize.md)
- [Nastavení samoobslužné verze portálu][2]

Procházet Další prostředky:

- [Úložiště GitHub se zdrojovým kódem][1]
- [Veřejný plán projektu][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects