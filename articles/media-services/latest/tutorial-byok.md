---
title: Klíče spravované zákazníkem nebo Přineste si vlastní klíč (BYOK) s Media Services
description: Toto je kurz použití spravovaných klíčů zákazníka s účtem úložiště Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c26da9d888bbcdf72c052fa4bd7fcdf443a2d8f7
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326137"
---
# <a name="tutorial-use-customer-managed-keys-or-bring-your-own-key-byok-with-media-services"></a>Kurz: použití klíčů spravovaných zákazníkem nebo Přineste si vlastní klíč (BYOK) s Media Services

S rozhraním API 2020-05-01 můžete použít spravovaný klíč RSA zákazníka s účtem Media Services, který má identitu spravovanou systémem.  V tomto kurzu se skládá kolekce a prostředí pro odesílání žádostí REST do služeb Azure.  Používané služby:

- Azure Active Directory registraci aplikace pro post
- Microsoft Graph API
- Azure Storage
- Azure Key Vault
- Media Services

V tomto kurzu se naučíte používat metodu post pro:

> [!div class="checklist"]
> * Získejte tokeny pro použití se službami Azure.
> * Vytvořte skupinu prostředků a účet úložiště.
> * Vytvoření účtu Media Services s identitou spravované systémem
> * Vytvořte Key Vault pro uložení klíče RSA (spravovaného zákazníkem) pro použití s účtem úložiště.
> * Aktualizujte účet Media Services, aby používal klíč RSA s účtem úložiště.
> * Použijte proměnné v post.

Pokud nemáte předplatné Azure, [Vytvořte si bezplatný zkušební účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

- Zaregistrujte instanční objekt s příslušnými oprávněními.
- Nainstalujte [post](https://www.postman.com).
- Stáhněte si kolekci post pro tento kurz v [ukázkách Azure: Media-Services-Customer-Managed-Keys-BYOK](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Registrace instančního objektu s potřebnými oprávněními

[Vytvoření instančního objektu](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).  Pokud chcete získat tajný klíč instančního objektu, podívejte se na [možnost 2](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options) .  Poznamenejte si tajný kód, protože když ponecháte tajnou stránku na portálu, nebude přístupná.

K provedení úkolů v tomto kurzu potřebuje instanční objekt následující oprávnění.

![oprávnění potřebná pro instanční objekt](./media/tutorial-byok/service-principal-permissions-1.png)

### <a name="install-postman"></a>Nainstalovat post

Pokud jste ještě nenainstalovali Poster pro použití s Azure, můžete ho získat na adrese [Postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>Stažení a import kolekce

Stáhněte si kolekci post pro tento kurz v [ukázkách Azure: Media-Services-Customer-Managed-Keys-BYOK](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

## <a name="installation-of-collection-and-environment"></a>Instalace kolekce a prostředí

1. Spusťte post.
1. Vyberte **Importovat**.
1. Vyberte **Odeslat soubory**.
1. Přejděte do umístění, kam jste uložili soubory kolekce a prostředí.
1. Vyberte soubor kolekce a prostředí.
1. Vyberte **Otevřít**.  (Zobrazí se upozornění, že soubory se neimportují jako rozhraní API, ale jako kolekce.  To je přesné.  To je to, co chcete.)
1. Tato kolekce se teď bude zobrazovat ve vašich kolekcích jako BYOK.
1. Proměnné prostředí se nyní zobrazí ve vašich prostředích.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Pochopení požadavků REST API v kolekci

Kolekce poskytuje následující požadavky REST API. Žádosti musí být odesílány v sekvenci, která je poskytována jako většina z nich, má testovací skripty, které dynamicky vytvoří globální proměnné pro další (nebo další) žádost v sekvenci. Není nutné ručně vytvářet globální proměnné.

V části post se tyto proměnné nacházejí v `{{ }}` závorkách.  Například, `{{bearerToken}}`.

1. Získat token AAD – test nastaví globální proměnnou *bearerToken*
2. Získat token grafu – test nastaví globální proměnnou *graphToken*
3. Získat podrobnosti objektu služby – test nastaví globální proměnnou *servicePrincipalObjectId*
4. Vytvoření účtu úložiště – test nastaví globální proměnnou *storageAccountId*
5. Vytvoření účtu Media Services se systémem spravovaná identita – test nastaví globální proměnnou *principalId*
6. Vytvoření Key Vault a udělení přístupu k instančnímu objektu – test nastaví globální proměnnou *keyVaultId*
7. Získání tokenu Key Vault – sada testů globální proměnné *keyVaultToken*
8. Vytvoření klíče RSA v trezoru klíčů – sada testů nastaví globální proměnnou *keyId*
9. Aktualizujte účet Media Services, aby používal klíč s účtem úložiště – pro tento požadavek není k dispozici testovací skript.

## <a name="define-environment-variables"></a>Definování proměnných prostředí

1. Přepněte do prostředí, které jste stáhli, a vyberte rozevírací seznam prostředí.
1. Vytvořte proměnné prostředí v post. Používají se také jako proměnné obsažené v `{{ }}` závorkách.  Například, `{{tenantId}}`.

    * *tenantId* = ID tenanta
    * *servicePrincipalId* = ID instančního objektu, který jste navázali pomocí oblíbené metody: portál, CLI a tak dále.
    * *servicePrincipalSecret* = tajný kód vytvořený pro instanční objekt
    * *předplatné* = ID vašeho předplatného
    * *Storage* = název, který chcete vašemu úložišti přidělit
    * název *účtu* : název účtu mediální služby, který chcete použít.
    * název *trezoru* = Key Vault název, který chcete použít
    * *resourceLocation* = Centralus (nebo kde někdy chcete prostředky umístit.  Tato kolekce byla testována pouze pomocí centralus.)
    * *resourceName* = název skupiny prostředků

    Následující proměnné jsou standard pro práci s prostředky Azure, takže je nemusíte měnit.

    * *armResource* = `https://management.core.windows.net`
    * *graphResource* = `https://graph.windows.net/`
    * *keyVaultResource* = `https://vault.azure.net`
    * *armEndpoint* = `management.azure.com`
    * *graphEndpoint* = `graph.windows.net`
    * *aadEndpoint* = `login.microsoftonline.com`
    * *keyVaultDomainSuffix* = `vault.azure.net`

## <a name="send-the-requests"></a>Odeslat žádosti

Po definování proměnných prostředí můžete buď spustit žádosti postupně ve výše uvedeném pořadí, nebo použít spouštěč post pro spuštění kolekce.

## <a name="change-the-key"></a>Změnit klíč

Služba Media Services automaticky detekuje, kdy byl klíč změněn.  Pokud to chcete otestovat, vytvořte pro stejný klíč jinou verzi klíče. Media Services by měl tento klíč detekovat za méně než 15 minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat prostředky, které jste vytvořili, a nechcete, aby se **vám nadále účtují**, odstraňte je.

## <a name="next-steps"></a>Další kroky

V dalším článku se naučíte, jak...
> [!div class="nextstepaction"]
> [Kódování vzdáleného souboru na základě adresy URL a streamování videa pomocí REST](stream-files-tutorial-with-rest.md)
