---
title: Použití klíčů spravovaných zákazníkem nebo BYOK REST API
description: V tomto kurzu použijete klíče spravované zákazníkem nebo Přineste vlastní klíč (BYOK) s účtem úložiště Azure Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: 93bbd84c25f02ac1653a46ebb4a70c1dfa90c744
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687230"
---
# <a name="tutorial-use-customer-managed-keys-or-byok-with-media-services-rest-api"></a>Kurz: použití klíčů spravovaných zákazníkem nebo BYOK s využitím Media Services REST API

S rozhraním API 2020-05-01 můžete použít klíč RSA spravovaný zákazníkem s účtem Azure Media Services, který má systémově spravovanou identitu. V tomto kurzu se skládá kolekce a prostředí pro odesílání žádostí REST do služeb Azure. Používají se tyto služby:

- Registrace aplikace Azure Active Directory (Azure AD) pro post
- Microsoft Graph API
- Azure Storage
- Azure Key Vault
- Azure Media Services

V tomto kurzu se naučíte používat metodu post pro:

> [!div class="checklist"]
> - Získejte tokeny pro použití se službami Azure.
> - Vytvořte skupinu prostředků a účet úložiště.
> - Vytvořte účet Media Services s identitou spravovanou systémem.
> - Vytvořte Trezor klíčů pro uložení klíče RSA spravovaného zákazníkem.
> - Aktualizujte účet Media Services, aby používal klíč RSA s účtem úložiště.
> - Použijte proměnné v post.

Pokud nemáte předplatné Azure, [Vytvořte si bezplatný zkušební účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

1. Zaregistrujte instanční objekt s příslušnými oprávněními.
1. Nainstalujte [post](https://www.postman.com).
1. Stáhněte si shromažďování po shromáždění pro tento kurz v [ukázkách Azure: Media-Services-Customer-Managed-Key-BYOK](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Registrace instančního objektu s potřebnými oprávněními

1. [Vytvoření instančního objektu](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).
1. Přejít na [možnost 2: vytvořením nového tajného klíče aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options) získáte tajný klíč objektu služby.

   > [!IMPORTANT]
   >Zkopírujte a uložte tajný klíč pro pozdější použití. Po opuštění stránky tajného kódu na portálu nemůžete získat přístup ke tajnému kódu.

1. Přiřaďte oprávnění k instančnímu objektu, jak je znázorněno na následujícím snímku obrazovky:

   :::image type="complex" source="./media/tutorial-byok/service-principal-permissions-1.png" alt-text="Snímek obrazovky zobrazující oprávnění potřebná pro instanční objekt":::
   Oprávnění jsou uvedena podle služby, název oprávnění, typ a popis. Azure Key Vault: zosobnění uživatele, delegovaný, úplný přístup k Azure Key Vault. Správa služeb Azure: zosobnění uživatele, delegovaný přístup ke správě služeb Azure jako uživatel organizace. Azure Storage: zosobnění uživatele, delegovaný přístup Azure Storage. Media Services: zosobnění uživatele, delegované, přístup k Media Services. Microsoft Graph: uživatel. Read, delegovaný, přihlaste se a číst profil uživatele.
   :::image-end:::

### <a name="install-postman"></a>Nainstalovat post

Pokud jste ještě nenainstalovali Poster pro použití s Azure, můžete ho získat na adrese [Postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>Stažení a import kolekce

Stáhněte si shromažďování po shromáždění pro tento kurz v [ukázkách Azure: Media-Services-Customer-Managed-Key-BYOK](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

## <a name="install-the-postman-collection-and-environment"></a>Instalace kolekce a prostředí pro odesílání

1. Spusťte post.
1. Vyberte **Importovat**.
1. Vyberte **Odeslat soubory**.
1. Přejít na místo, kam jste uložili soubory kolekce a prostředí.
1. Vyberte kolekci a soubory prostředí.
1. Vyberte **Otevřít**. Zobrazí se upozornění oznamující, že soubory nebudou importovány jako rozhraní API, ale jako kolekce. Toto upozornění je přesné. Je to, co chcete.

Kolekce se nyní zobrazuje ve vašich kolekcích jako BYOK. Ve vašem prostředí se taky zobrazí proměnné prostředí.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Pochopení požadavků REST API v kolekci

Kolekce poskytuje následující požadavky REST API.

> [!NOTE]
>
>- Žádosti musí být odesílány v zadaném pořadí.
>- Většina požadavků má testovací skripty, které dynamicky vytváří globální proměnné pro další požadavek v sekvenci.
>- Nemusíte vytvářet globální proměnné ručně.

V části post se tyto proměnné nacházejí v závorkách. Například, `{{bearerToken}}`.

1. Získání tokenu Azure AD: test nastaví globální proměnnou **bearerToken**.
2. Získání Microsoft Graph tokenu: test nastaví globální proměnnou **graphToken**.
3. Získat podrobnosti objektu služby: test nastaví globální proměnnou **servicePrincipalObjectId**.
4. Vytvoření účtu úložiště: test nastaví globální proměnnou **storageAccountId**.
5. Vytvoření účtu Media Services s identitou spravovanou systémem: test nastaví globální proměnnou **principalId**.
6. Vytvoření trezoru klíčů pro udělení přístupu k instančnímu objektu: test nastaví globální proměnnou **keyVaultId**.
7. Získání Key Vault tokenu: test nastaví globální proměnnou **keyVaultToken**.
8. Vytvoření klíče RSA v trezoru klíčů: test nastaví globální proměnnou **keyId**.
9. Aktualizujte účet Media Services, aby používal klíč s účtem úložiště: pro tento požadavek není k dispozici žádný testovací skript.

## <a name="define-environment-variables"></a>Definování proměnných prostředí

1. Vyberte rozevírací seznam prostředí a přepněte se do prostředí, které jste stáhli.
1. Vytvořte proměnné prostředí v post. Používají se také jako proměnné obsažené v závorkách. Například, `{{tenantId}}`.

    - **tenantId**: vaše ID tenanta.
    - **servicePrincipalId**: ID instančního objektu, který jste navázali s vaší oblíbenou metodou, jako je například portál nebo rozhraní příkazového řádku.
    - **servicePrincipalSecret**: tajný kód vytvořený pro instanční objekt.
    - **předplatné**: ID vašeho předplatného.
    - **Storage (úložiště**): název, který chcete vašemu úložišti přidělit.
    - název **účtu**: Media Services název účtu, který chcete použít.
    - název **trezoru klíčů: název** trezoru klíčů, který chcete použít.
    - **resourceLocation**: umístění **CentralUs** nebo kam chcete prostředky umístit. Tato kolekce byla testována pouze pomocí **CentralUs**.
    - Skupina prostředků **zdroj**: název skupiny prostředků.

    Následující proměnné jsou standard pro práci s prostředky Azure. Takže je nemusíte měnit.

    - **armResource**: `https://management.core.windows.net`
    - **graphResource**: `https://graph.windows.net/`
    - **keyVaultResource**: `https://vault.azure.net`
    - **armEndpoint**: `management.azure.com`
    - **graphEndpoint**: `graph.windows.net`
    - **aadEndpoint**: `login.microsoftonline.com`
    - **keyVaultDomainSuffix**: `vault.azure.net`

## <a name="send-the-requests"></a>Odeslat žádosti

Po definování proměnných prostředí můžete spustit žádosti po jednom v předchozí sekvenci. Nebo můžete použít spouštěčer pro spuštění kolekce.

## <a name="change-the-key"></a>Změnit klíč

Media Services automaticky detekuje, kdy se klíč změnil. Pro otestování tohoto procesu vytvořte další verzi klíče pro stejný klíč. Media Services by měl klíč detekovat za méně než 15 minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat prostředky, které jste vytvořili, a nechcete, aby se *vám nadále účtují*, odstraňte je.

## <a name="next-steps"></a>Další kroky

Podívejte se na další článek, kde se dozvíte, jak:
> [!div class="nextstepaction"]
> [Kódování vzdáleného souboru na základě adresy URL a streamování videa pomocí REST](stream-files-tutorial-with-rest.md)
