---
title: Přidání a ověření vlastních názvů domén – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Koncepty správy a návody pro správu názvu domény ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2395aa5984de2a9fe41e4778d16aba69bfef5192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559229"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Správa vlastních názvů domén ve službě Azure Active Directory

Název domény je důležitou součástí identifikátoru pro mnoho prostředků adresáře: je součástí uživatelského jména nebo e-mailové adresy uživatele, součástí adresy skupiny a někdy je součástí identifikátoru URI id aplikace pro aplikaci. Prostředek ve službě Azure Active Directory (Azure AD) může obsahovat název domény, který je vlastněn adresářem, který obsahuje prostředek. Domény ve službě Azure AD může spravovat jenom globální správce.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Nastavení primárního názvu domény pro adresář Azure AD

Po vytvoření adresáře je primárnínázev domény, například contoso.onmicrosoft.com. Primární doména je při vytváření nového uživatele výchozím názvem domény nového uživatele. Nastavení primárního názvu domény zjednodušuje proces vytváření nových uživatelů na portálu pro správce. Změna názvu primární domény:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu, který je globálním správcem adresáře.
2. Vyberte **Azure Active Directory**.
3. Vyberte **Názvy vlastních domén**.
  
   ![Otevření stránky správy uživatelů](./media/domains-manage/add-custom-domain.png)
4. Vyberte název domény, která má být primární doménou.
5. Vyberte příkaz **Vytvořit primární.** Po zobrazení výzvy potvrďte svou volbu.
  
   ![Nastavení primárního názvu domény](./media/domains-manage/make-primary-domain.png)

Primární název domény pro váš adresář můžete změnit tak, aby se u vás nezobrazovala ověřená vlastní doména, která není federovaná. Změna primární domény adresáře nezmění uživatelské jméno pro stávající uživatele.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Přidání vlastních názvů domén do klienta Azure AD

Můžete přidat až 900 spravovaných doménových jmen. Pokud konfigurujete všechny domény pro federaci pomocí místní služby Active Directory, můžete v každém adresáři přidat až 450 názvů domén.

## <a name="add-subdomains-of-a-custom-domain"></a>Přidání subdomén vlastní domény

Pokud chcete do adresáře přidat název domény třetí úrovně, například "europe.contoso.com", měli byste nejprve přidat a ověřit doménu druhé úrovně, například contoso.com. Subdoména se automaticky ověří službou Azure AD. Chcete-li zjistit, zda je přidána subdoména, aktualizujte seznam domén v prohlížeči.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Co dělat, když změníte registrátora DNS pro vlastní název domény

Pokud změníte registrátory DNS, neexistují žádné další úlohy konfigurace ve službě Azure AD. Můžete pokračovat v používání názvu domény s Azure AD bez přerušení. Pokud používáte vlastní název domény s Office 365, Intune nebo jinými službami, které jsou závislé na vlastních názvech domén ve službě Azure AD, podívejte se do dokumentace k těmto službám.

## <a name="delete-a-custom-domain-name"></a>Odstranění vlastního názvu domény

Vlastní název domény můžete odstranit z azure ad, pokud vaše organizace již používá tento název domény, nebo pokud potřebujete použít tento název domény s jiným Azure AD.

Chcete-li odstranit vlastní název domény, musíte nejprve zajistit, aby na název domény nespoléhaly žádné prostředky ve vašem adresáři. Název domény z adresáře nelze odstranit, pokud:

* Každý uživatel má uživatelské jméno, e-mailovou adresu nebo adresu proxy, která obsahuje název domény.
* Každá skupina má e-mailovou adresu nebo adresu proxy, která obsahuje název domény.
* Každá aplikace ve vašem Azure AD má identifikátor URI ID aplikace, který obsahuje název domény.

Před odstraněním vlastního názvu domény je nutné změnit nebo odstranit jakýkoli takový prostředek v adresáři Azure AD.

### <a name="forcedelete-option"></a>Možnost ForceDelete

Název domény můžete **použít forcedelete** v [Centru pro správu Azure AD](https://aad.portal.azure.com) nebo pomocí [rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta). Tyto možnosti používají asynchronní operaci a aktualizují všechny odkazyuser@contoso.comz vlastního názvu domény,user@contoso.onmicrosoft.comnapříklad " " na původní výchozí název domény, například " . 

Chcete-li volat **ForceDelete** na webu Azure portal, musíte zajistit, že existuje méně než 1000 odkazů na název domény a všechny odkazy, kde Exchange je zřizovací služba musí být aktualizovány nebo odebrány v [Centru pro správu Exchange](https://outlook.office365.com/ecp/). To zahrnuje skupiny zabezpečení s povolenou poštou exchange a distribuované seznamy; Další informace naleznete v [tématu Odebrání skupin zabezpečení s povolenou poštou](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). Operace **ForceDelete** také nebude úspěšná, pokud je splněna některá z následujících podmínek:

* Doménu jste zakoupili prostřednictvím služeb předplatného domény Office 365
* Jste partner, který spravuje jménem jiného klienta zákazníka.

Následující akce jsou prováděny jako součást **forcedelete** operace:

* Přejmenuje hlavní název uživatele, e-mailovou adresu a adresu ProxyAddress uživatele s odkazy na vlastní název domény na původní výchozí název domény.
* Přejmenuje e-mailovou adresu skupin s odkazy na vlastní název domény na původní výchozí název domény.
* Přejmenuje identifikátorUris aplikací s odkazy na vlastní název domény na původní výchozí název domény.

Chyba je vrácena, když:

* Počet objektů, které mají být přejmenovány, je větší než 1000
* Jednou z aplikací, které mají být přejmenovány, je víceklientská aplikace

### <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Proč se odstranění domény nedaří s chybou, která uvádí, že mám exchange mastered skupiny na tento název domény?** <br>
**A:** Některé skupiny, jako jsou skupiny zabezpečení s podporou pošty a distribuované seznamy, jsou dnes zřízeny serverem Exchange a je třeba je ručně vyčistit v [Centru pro správu exchange (EAC).](https://outlook.office365.com/ecp/) Mohou existovat přetrvávající ProxyAddresses, které spoléhají na vlastní název domény a bude nutné aktualizovat ručně na jiný název domény. 

**Otázka: Jsem přihlášen jako správce\@contoso.com ale nemohu odstranit název domény "contoso.com"?**<br>
**A:** V názvu uživatelského účtu nelze odkazovat na vlastní název domény, který se pokoušíte odstranit. Ujistěte se, že účet globálního správce používá počáteční výchozí admin@contoso.onmicrosoft.comnázev domény (.onmicrosoft.com), například . Přihlaste se pomocí jiného admin@contoso.onmicrosoft.com účtu globálního správce, který například nebo admin@fabrikam.comjiný vlastní název domény, například "fabrikam.com", kde je účet .

**Otázka: Kliknul jsem na tlačítko `In Progress` Odstranit doménu a zhlédnul stav operace Odstranit. Jak dlouho to trvá? Co se stane, když selže?**<br>
**A:** Operace odstranění domény je asynchronní úloha na pozadí, která přejmenuje všechny odkazy na název domény. Mělo by to být hotové během minuty nebo dvou. Pokud se odstranění domény nezdaří, ujistěte se, že nemáte:

* Aplikace nakonfigurované na názvu domény pomocí aplikace IdentifierURI
* Libovolná skupina s povolenou poštou odkazující na vlastní název domény
* Více než 1000 odkazů na název domény

Pokud zjistíte, že některé z podmínek nebyly splněny, ručně vyčistěte odkazy a zkuste odstranit doménu znovu.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Správa názvů domén pomocí prostředí PowerShell nebo rozhraní Microsoft Graph API

Většinu úloh správy pro názvy domén ve službě Azure Active Directory lze také dokončit pomocí prostředí Microsoft PowerShell nebo programově pomocí rozhraní Microsoft Graph API.

* [Správa názvů domén ve službě Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#domains)
* [Typ prostředku domény](https://docs.microsoft.com/graph/api/resources/domain?view=graph-rest-1.0)

## <a name="next-steps"></a>Další kroky

* [Přidání vlastních názvů domén](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Odebrání skupin zabezpečení s povoleným e-mailem exchange v Centru pro správu Exchange s vlastním názvem domény ve službě Azure AD](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [ForceDelete vlastní název domény s rozhraním Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
