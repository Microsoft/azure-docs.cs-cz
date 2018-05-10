---
title: Přidat vlastní doménu do služby Azure AD | Microsoft Docs
description: Vysvětluje, jak přidat vlastní doménu v Azure Active Directory.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 37890ac856a6b6bd89acc17973327b239a583997
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>Rychlý úvod: Přidání vlastního názvu domény do Azure Active Directory

Každý adresář Azure AD se dodává s počáteční název domény ve formě *domainname*. onmicrosoft.com. Název domény nelze změnit ani odstranit, ale můžete přidat název vaší firemní domény do Azure AD i. Například vaše organizace nemá pravděpodobně jiné názvy domén, umožňuje udělat firmy a uživatele, kteří se přihlašují pomocí firemního názvu domény. Přidání vlastních názvů domén do Azure AD umožňuje přiřazovat uživatelská jména v adresáři, které jsou pro uživatele, jako například 'alice@contoso.com. " místo ' alice @*název domény*. onmicrosoft.com ". Proces je jednoduchý:

1. Přidání vlastního názvu domény do adresáře
2. Přidání položky DNS pro název domény u registrátora názvu domény
3. Ověření vlastního názvu domény v Azure AD

## <a name="add-the-custom-domain-name-to-your-directory"></a>Přidání vlastního názvu domény do adresáře
1. Přihlaste se k [portál Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí účtu, který je globální správce adresáře.
2. Na levé straně vyberte **vlastní názvy domén**.
3. Vyberte **přidat vlastní doménu**.
   
   ![Vyberte příkaz Přidat](./media/add-custom-domain/add-custom-domain.png)
5. Na **vlastní názvy domén**, do pole, jako je například "contoso.com", zadejte název vlastní domény a pak vyberte **přidat doménu**. Nezapomeňte napsat i příponu .com, .net nebo jinou příponu nejvyšší úrovně.
6. Na ***domainname*** (to znamená, že nový název domény je název), shromážděte informace záznam DNS pro pozdější použití ověřit vlastní název domény ve službě Azure AD.
   
   ![získat informace o záznam DNS.](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> Pokud máte v úmyslu vytvořit federaci místní Windows Server AD s Azure AD, pak je nutné vybrat **I v úmyslu konfigurovat tuto doménu pro jednotné přihlašování s Moje místní služby Active Directory** políčko při spuštění nástroje Azure AD Connect pro synchronizaci adresářů. Musíte také zaregistrovat se stejným názvem domény, vyberte pro federaci s místní adresáře **Azure AD Domain** krok v průvodci. [V těchto pokynech](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation) uvidíte, jak daný krok v průvodci vypadá. Pokud nástroj Azure AD Connect nemáte, můžete [ho stáhnout tady](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-a-dns-entry-for-the-domain-name-at-the-domain-name-registrar"></a>Přidání položky DNS pro název domény u registrátora názvu domény
Pokud chcete používat vlastní název domény ve službě Azure AD, dalším krokem je aktualizace souboru zóny DNS pro takovou doménu. Azure AD můžete pak ověřte, že vaše organizace vlastní vlastní název domény. Můžete použít [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) pro svoje záznamy DNS Azure nebo Office 365 nebo externí v rámci Azure, nebo přidejte položku DNS na [různých registrátora DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Přihlaste se k registrátorovi názvu domény. Pokud k aktualizaci položky DNS nemáte přístup, požádejte osobu nebo tým, kteří přístup mají, aby dokončili krok 2 a dali vám vědět, až bude hotový.
2. Aktualizujte soubor zóny DNS pro doménu tím, že přidáte položku DNS, kterou jste získali od Azure AD. Položka DNS neovlivní žádné chování, například směrování pošty nebo webhosting.

## <a name="verify-the-custom-domain-name-in-azure-ad"></a>Ověření vlastního názvu domény v Azure AD
Po přidání položky DNS jste připraveni na ověření názvu domény pomocí Azure AD. Název domény můžete ověřit, až poté, co jste rozšíří záznamy DNS. Šíření často trvá jen několik sekund, ale občas může zabrat i hodinu nebo déle. Pokud ověření na první pokus nefunguje, zkuste to později.

1. Přihlaste se k [Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) pomocí účtu, který je globální správce pro tenanta.
2. Vyberte **vlastní názvy domén**.
3. Vyberte název neověřené domény, který chcete ověřit.
4. Zkontrolujte zadání a vyberte **ověřte** a dokončete ověření.

Teď můžete [přiřazovat uživatelská jména, která obsahují vlastní název domény](active-directory-users-create-azure-portal.md). Můžete vytvořit cloudový uživatelské účty nebo aktualizace dříve synchronizovaných položek místní informace o uživatelském účtu, pomocí vlastního názvu domény. Můžete také změnit, synchronizovaný uživatelského účtu domény příponu informace pomocí [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) nebo [rozhraní Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

> [!TIP]
> Můžete přidat na maximálně 900 názvů spravované domény. Pokud konfigurujete všechny domény pro federační místní službou Active Directory, můžete přidat k maximální 450 názvů domén v každý adresář. Další informace najdete v tématu [federovaný a názvy spravované domény](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="troubleshooting"></a>Řešení potíží
Pokud nemůžete ověřit vlastní název domény, zkuste následující kroky řešení potíží:

1. **Počkejte hodinu**. Záznamy DNS musí rozšířit před Azure AD můžete ověřit doménu. Tento proces může trvat hodinu i déle.
2. **Zkontrolujte, jestli je zadán záznam DNS a jestli je správně**. Tento krok proveďte na webu registrátora názvu domény. Azure AD nelze ověřit název domény, pokud 
  * Položku DNS není k dispozici v souboru zóny DNS
  * Položka DNS se přesně neshoduje s položkou DNS, kterou vám poskytla služba Azure AD. 
  
  Pokud nemáte přístup k aktualizaci záznamů DNS domény u registrátora názvu domény, poskytněte položku DNS osobě nebo týmu z vaší organizace, kteří tento přístup mají, a požádejte je, aby položku DNS přidali.
3. **Odstraňte název domény z jiného adresáře ve službě Azure AD**. Název domény můžete ověřit jenom v jediném adresáři. Pokud se název domény je aktuálně ověřit v jiném adresáři, ho nelze ověřit v adresáři nové dokud je neodstraní na jiný. Další informace o odstraňování názvů domén najdete v článku [Správa vlastních názvů domén](active-directory-domains-manage-azure-portal.md).    

Opakujte kroky v tomto článku pro přidání jednotlivých názvů domén.

## <a name="learn-more"></a>Další informace
[Koncepční přehled vlastních názvů domén ve službě Azure AD](active-directory-domains-manage-azure-portal.md)

[Správa vlastních názvů domén](active-directory-domains-manage-azure-portal.md)

## <a name="next-steps"></a>Další postup
V tento rychlý start když jste se naučili jak přidat vlastní doménu do služby Azure AD. 

Na následující odkaz můžete přidat nové vlastní domény ve službě Azure AD na portálu Azure.

> [!div class="nextstepaction"]
> [Přidání vlastní domény](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 