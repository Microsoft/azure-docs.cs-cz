---
title: Řešení potíží s přihlášením k registru
description: Příznaky, příčiny a řešení běžných potíží při přihlašování do služby Azure Container Registry
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 5deb1717cf3886d8ea9c021d92afa358946b16dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052074"
---
# <a name="troubleshoot-registry-login"></a>Řešení potíží s přihlášením k registru

Tento článek vám pomůže při řešení problémů, se kterými se můžete setkat při přihlašování do služby Azure Container Registry. 

## <a name="symptoms"></a>Příznaky

Může zahrnovat jednu nebo více z následujících možností:

* Nelze se přihlásit k registru pomocí `docker login` , `az acr login` nebo obojího.
* Nepovedlo se přihlásit k registru a zobrazí `unauthorized: authentication required` se chyba. `unauthorized: Application not registered with AAD`
* Nepovedlo se přihlásit k registru a obdržíte chybu Azure CLI. `Could not connect to the registry login server`
* Nepovedlo se odeslat nebo načíst image a zobrazí se chyba Docker. `unauthorized: authentication required`
* Nejde získat přístup ke registru ze služby Azure Kubernetes, Azure DevOps nebo jiné služby Azure.
* Nejde získat přístup k registru a zobrazí se chyba `Error response from daemon: login attempt failed with status: 403 Forbidden` – Podívejte se na téma [řešení potíží se sítí pomocí registru](container-registry-troubleshoot-access.md) .
* Nejde získat přístup k nastavení registru v Azure Portal nebo spravovat registr pomocí rozhraní příkazového řádku Azure CLI.

## <a name="causes"></a>Příčiny

* Docker není ve vašem prostředí správně nakonfigurovaný – [řešení](#check-docker-configuration)
* Registr neexistuje nebo je název nesprávný – [řešení](#specify-correct-registry-name)
* Přihlašovací údaje registru nejsou platné – [řešení](#confirm-credentials-to-access-registry)
* Přihlašovací údaje nejsou autorizované pro operace push, Pull nebo Azure Resource Manager – [řešení](#confirm-credentials-are-authorized-to-access-registry)
* Platnost přihlašovacích údajů vypršela – [řešení](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>Další Diagnostika 

Pokud chcete získat další informace o stavu prostředí registru a volitelně získat přístup k cílovému registru, spusťte příkaz [AZ ACR check-Health](/cli/azure/acr#az-acr-check-health) . Například Diagnostikujte chyby konfigurace Docker nebo Azure Active Directory problémy s přihlášením. 

Příklady příkazů najdete v tématu o [kontrole stavu služby Azure Container Registry](container-registry-check-health.md) . Pokud dojde k chybám, přečtěte si [referenční informace o chybě](container-registry-health-error-reference.md) a v následujících oddílech, kde najdete doporučená řešení.

Pokud máte potíže s použitím služby Registry wih Azure Kubernetes, spusťte pomocí příkazu [AZ AKS check-ACR](/cli/azure/aks#az_aks_check_acr) , zda je registr přístupný z clusteru AKS.

> [!NOTE]
> Pokud existují konfigurace brány firewall nebo sítě, které brání přístupu k registru, může dojít k nějakým chybám ověřování nebo autorizaci taky. Přečtěte si téma [řešení potíží se sítí pomocí registru](container-registry-troubleshoot-access.md).

## <a name="potential-solutions"></a>Možná řešení

### <a name="check-docker-configuration"></a>Ověřit konfiguraci Docker

Většina Azure Container Registrych ověřovacích toků vyžaduje místní instalaci Docker, abyste se mohli ověřit pomocí registru pro operace, jako je například doručování a přijímání imagí. Ujistěte se, že klient rozhraní příkazového řádku Docker CLI a démon (Docker Engine) běží ve vašem prostředí. Potřebujete klienta Docker verze 18,03 nebo novější.

Související odkazy:

* [Přehled ověřování](container-registry-authentication.md#authentication-options)
* [Nejčastější dotazy k registru kontejneru](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>Zadat správný název registru

Při použití `docker login` Zadejte úplný název přihlašovacího serveru registru, například *myregistry.azurecr.IO*. Ujistěte se, že používáte jenom malá písmena. Příklad:

```console
docker login myregistry.azurecr.io
```

Když používáte [AZ ACR Login](/cli/azure/acr#az-acr-login) s identitou Azure Active Directory, nejdřív se [přihlaste do Azure CLI](/cli/azure/authenticate-azure-cli)a pak zadejte název prostředku Azure registru. Název prostředku je název, který jste zadali při vytvoření registru, například *myregistry* (bez přípony domény). Příklad:

```azurecli
az acr login --name myregistry
```

Související odkazy:

* [AZ ACR Login se zdaří, ale Docker selže s chybou: Neautorizováno: vyžaduje se ověření.](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required )

### <a name="confirm-credentials-to-access-registry"></a>Potvrďte přihlašovací údaje pro přístup k registru

Ověřte platnost přihlašovacích údajů, které jste ve svém scénáři použili, nebo vám poskytl vlastník registru. Některé možné problémy:

* Pokud používáte instanční objekt služby Active Directory, ujistěte se, že používáte správné přihlašovací údaje v tenantovi služby Active Directory:
  * Uživatelské jméno – ID aplikace instančního objektu (označuje se také jako *ID klienta*)
  * Heslo – hlavní heslo služby (označuje se taky jako *tajný klíč klienta*)
* Pokud k registru přistupujete pomocí služby Azure, jako je služba Azure Kubernetes nebo Azure DevOps, potvrďte konfiguraci registru pro vaši službu. 
* Pokud jste spustili `az acr login` s `--expose-token` možností, která umožňuje přihlášení do registru bez použití démona Docker, ujistěte se, že jste s uživatelským jménem ověřili `00000000-0000-0000-0000-000000000000` .
* Pokud je váš registr nakonfigurovaný pro [anonymní přístup s přístupem k přístupu](container-registry-faq.md#how-do-i-enable-anonymous-pull-access), může anonymní přístup zabránit existujícím přihlašovacím údajům z doku uložených z předchozího přihlášení k dokovacímu zařízení. Spusťte `docker logout` před pokusem o anonymní operaci Pull v registru.

Související odkazy:

* [Přehled ověřování](container-registry-authentication.md#authentication-options)
* [Individuální přihlášení pomocí Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)
* [Přihlášení pomocí instančního objektu](container-registry-auth-service-principal.md)
* [Přihlášení se spravovanou identitou](container-registry-authentication-managed-identity.md)
* [Přihlášení s tokenem s oborem úložiště](container-registry-repository-scoped-permissions.md)
* [Přihlášení s účtem správce](container-registry-authentication.md#admin-account)
* [Kódy chyb ověřování a autorizace Azure AD](../active-directory/develop/reference-aadsts-error-codes.md)
* [AZ ACR Login](/cli/azure/acr#az-acr-login) reference

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Ověření přihlašovacích údajů má oprávnění pro přístup k registru.

Potvrďte oprávnění registru, která jsou přidružená k přihlašovacím údajům, jako je například `AcrPull` role Azure pro vyžádání imagí z registru nebo `AcrPush` role pro vložení imagí. 

Přístup k registru v portálu nebo správě registru pomocí rozhraní příkazového řádku Azure CLI vyžaduje aspoň `Reader` roli nebo ekvivalentní oprávnění k provádění operací Azure Resource Manager.

Pokud se vaše oprávnění v nedávné době změnila tak, aby povolovala přístup k registru i přes portál, možná budete muset v prohlížeči vyzkoušet anonymním nebo soukromou relaci, aby nedocházelo k zastaralým mezipaměťm prohlížeče nebo souborům cookie

K přidání nebo odebrání přiřazení rolí musí mít vlastník v rámci předplatného dostatečná oprávnění.

Související odkazy:

* [Role a oprávnění Azure – Azure Container Registry](container-registry-roles.md)
* [Přihlášení s tokenem s oborem úložiště](container-registry-repository-scoped-permissions.md)
* [Přidání nebo odebrání přiřazení rolí Azure pomocí portálu Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Vytvoření aplikace Azure AD a instančního objektu s přístupem k prostředkům pomocí portálu](../active-directory/develop/howto-create-service-principal-portal.md)
* [Vytvoření nového tajného klíče aplikace](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Ověřování a autorizační kódy Azure AD](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Ověřte platnost přihlašovacích údajů ještě nevypršela.

Tokeny a přihlašovací údaje služby Active Directory můžou po definovaných obdobích vypršet, což zabrání přístupu do registru. Pokud chcete povolit přístup, může být potřeba resetovat nebo znovu vygenerovat přihlašovací údaje.

* Pokud používáte individuální identitu AD, spravovanou identitu nebo instanční objekt pro přihlášení k registru, vyprší platnost tokenu služby AD po 3 hodinách. Přihlaste se znovu k registru.  
* Pokud používáte instanční objekt služby Active Directory s neplatným tajným kódem klienta, musí správce předplatného nebo správce účtu resetovat přihlašovací údaje nebo vygenerovat nový objekt služby.
* Pokud používáte [token s rozsahem úložiště](container-registry-repository-scoped-permissions.md), vlastník registru může potřebovat resetovat heslo nebo vygenerovat nový token.

Související odkazy:

* [Resetovat přihlašovací údaje instančního objektu](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
* [Znovu vygenerovat hesla tokenů](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Individuální přihlášení pomocí Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Řešení potíží na pokročilé úrovni

Pokud je v registru povolená [kolekce protokolů prostředků](container-registry-diagnostics-audit-logs.md) , přečtěte si protokol ContainterRegistryLoginEvents. Tento protokol ukládá události a stav ověřování, včetně příchozí identity a IP adresy. Dotaz na protokol pro [selhání ověřování registru](container-registry-diagnostics-audit-logs.md#registry-authentication-failures). 

Související odkazy:

* [Protokoly pro vyhodnocení a auditování diagnostiky](container-registry-diagnostics-audit-logs.md)
* [Nejčastější dotazy k registru kontejneru](container-registry-faq.md)
* [Osvědčené postupy pro službu Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Další kroky

Pokud se vám problém nevyřeší, přečtěte si následující možnosti.

* Mezi další témata týkající se řešení potíží registru patří:
  * [Řešení potíží se sítí pomocí registru](container-registry-troubleshoot-access.md)
  * [Řešení potíží s výkonem registru](container-registry-troubleshoot-performance.md)
* Možnosti [podpory komunity](https://azure.microsoft.com/support/community/)
* [Microsoft – otázky a odpovědi](/answers/products/)
* [Otevření lístku podpory](https://azure.microsoft.com/support/create-ticket/) založeného na informacích, které poskytnete, může být spuštění rychlé diagnostiky pro chyby ověřování v registru.