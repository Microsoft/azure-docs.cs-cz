---
title: Sestavy aktivit přihlašování Azure Active Directory – Preview | Microsoft Docs
description: Seznámení se sestavami aktivit přihlašování na portálu Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/16/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 781cafd9b382868d0aa4f6b77ff7338c4ee15ed2
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589639"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Sestavy aktivit přihlašování Azure Active Directory – Preview

Portál Azure Active Directory umožňuje přístup k třem protokolům aktivit:

- **Přihlášení** – informace o přihlášeních a o tom, jak vaše prostředky používají vaši uživatelé.
- **[Audit](concept-audit-logs.md)** – informace o změnách použitých pro vašeho tenanta, jako jsou uživatelé a Správa skupin nebo aktualizace, které se vztahují na prostředky vašeho tenanta.
- **[Zřizování](concept-provisioning-logs.md)** – aktivity prováděné službou zřizování, například vytvoření skupiny v ServiceNow nebo uživatel importovaný z pracovního dne.


Sestava klasického přihlášení v Azure Active Directory poskytuje přehled interaktivních přihlášení uživatelů. Kromě toho máte nyní přístup k třem dalším sestavám přihlašování, které jsou nyní ve verzi Preview:

- Přihlášení bez interaktivního uživatele

- Přihlašovací objekty instančního objektu

- Spravované identity pro přihlášení prostředků Azure

Tento článek poskytuje přehled sestavy aktivit přihlašování s náhledem neinteraktivních, aplikací a spravovaných identit pro přihlášení prostředků Azure. Informace o přihlašovacích sestavách bez funkcí verze Preview najdete v tématu  [sestavy aktivit přihlašování na portálu Azure Active Directory](concept-sign-ins.md).



## <a name="prerequisites"></a>Požadavky

Než budete moct začít používat tuto funkci, měli byste znát odpovědi na tyto otázky:

- Kdo má přístup k datům?

- Jaká licence Azure AD je potřeba pro přístup k přihlašovací aktivitě?

### <a name="who-can-access-the-data"></a>Kdo má přístup k datům?

- Uživatelé v rolích správce zabezpečení, čtenář zabezpečení a čtenář sestav

- Globální správci

- Každý uživatel (bez oprávnění správce) může přistupovat k vlastnímu přihlašování. 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaká licence Azure AD je potřeba pro přístup k přihlašovací aktivitě?

K vašemu tenantovi musí být přidružená licence Azure AD Premium, aby bylo možné zobrazit přihlašovací aktivity. Pokud chcete upgradovat edici Azure Active Directory, přečtěte si téma [Začínáme se Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) . Bude trvat několik dní, než se data zobrazí v sestavách po upgradu na licenci Premium bez jakýchkoli aktivit dat před upgradem.



## <a name="sign-ins-report"></a>Sestava přihlášení

Sestava přihlášení obsahuje odpovědi na následující otázky:

- Jaký je vzor přihlášení uživatele, aplikace nebo služby?
- Kolik uživatelů, aplikací nebo služeb se přihlásilo za týden?
- Jaký je stav těchto přihlášení?


V okně sestavy přihlášení můžete přepínat mezi:

- **Přihlášení interaktivního uživatele** – přihlášení, kde uživatel poskytuje ověřovací faktor, jako je třeba heslo, odpověď prostřednictvím aplikace MFA, biometrické faktor nebo kód QR.

- **Přihlášení bez interaktivního uživatele** – přihlášení prováděná klientem jménem uživatele. Tato přihlášení nevyžadují od uživatele žádnou interakci ani ověřovací faktor. Například ověřování a autorizace pomocí aktualizačních a přístupových tokenů, které nevyžadují, aby uživatel zadal přihlašovací údaje.

- **Přihlášení instančního objektu** – přihlášení pomocí aplikací a instančních objektů, které nezahrnují žádného uživatele. V těchto přihlášeních aplikace nebo služba poskytují svým jménem vlastní přihlašovací údaje k ověřování nebo přístupu k prostředkům.

- **Spravované identity pro přihlášení prostředků Azure** – přihlášení pomocí prostředků Azure, které mají tajné klíče spravované v Azure Další informace najdete v tématu [co jsou spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md) 


![Typy sestav přihlášení](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins&quot;></a>Přihlášení uživatelů

Každá karta v okně přihlášení zobrazuje výchozí sloupce níže. Některé karty mají další sloupce:

- Datum přihlášení

- ID požadavku

- Uživatelské jméno nebo ID uživatele

- Název aplikace nebo ID aplikace

- Stav přihlášení

- IP adresa zařízení, které se používá pro přihlášení



### <a name=&quot;interactive-user-sign-ins&quot;></a>Přihlášení interaktivního uživatele


Přihlášení interaktivního uživatele jsou přihlášení, kde uživatel poskytuje ověřovací faktor pro Azure AD nebo komunikuje přímo se službou Azure AD nebo pomocnou aplikací, jako je například aplikace Microsoft Authenticator. Mezi faktory, které uživatelé poskytují, patří hesla, odpovědi na výzvy MFA, biometrické faktory nebo kódy QR, které uživatel poskytuje pro Azure AD nebo pro pomocnou aplikaci.

> [!NOTE]
> Tato sestava zahrnuje taky federované přihlašování od zprostředkovatelů identity, kteří jsou federované pro Azure AD.  



> [!NOTE] 
> Sestava interaktivních uživatelů, která se používá k zahrnutí některých neinteraktivních přihlášení od klientů Microsoft Exchange. I když tato přihlášení nejsou interaktivní, byly do sestavy přihlášení interaktivního uživatele zahrnuty pro další viditelnost. Po zadání sestavy neinteraktivních uživatelských přihlášení do verze Public Preview v listopadu 2020 se tyto protokoly událostí neinteraktivního přihlašování přesunuly do sestavy přihlášení neinteraktivního uživatele, aby se zvýšila přesnost. 


**Velikost sestavy:** malá <br> 
**4.6**

- Uživatel zadá uživatelské jméno a heslo na přihlašovací obrazovce služby Azure AD.

- Uživatel projde výzvou SMS MFA.

- Uživatel poskytuje biometrické gesto k odemknutí počítače s Windows pomocí Windows Hello pro firmy.

- Uživatel je federovaný pro Azure AD pomocí AD FS kontrolního výrazu SAML.


Kromě výchozích polí se v sestavě interaktivní přihlášení zobrazují také tyto položky: 

- Přihlašovací umístění

- Zda byl použit podmíněný přístup



Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Sloupce přihlášení interaktivního uživatele](./media/concept-all-sign-ins/columns-interactive.png &quot;Sloupce přihlášení interaktivního uživatele")





Přizpůsobení zobrazení umožňuje zobrazit další pole nebo odebrat pole, která jsou již zobrazena.

![Všechny interaktivní sloupce](./media/concept-all-sign-ins/all-interactive-columns.png)


Kliknutím na položku v zobrazení seznamu získáte podrobnější informace o souvisejícím přihlášení.

![Přihlašovací aktivita](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Přihlášení interaktivního uživatele")



### <a name="non-interactive-user-sign-ins"></a>Přihlášení bez interaktivního uživatele

Neinteraktivní přihlášení uživatelů jsou přihlášení, která provedla klientské aplikace nebo součásti operačního systému jménem uživatele. Podobně jako interaktivní přihlášení uživatelů se tato přihlášení provádí jménem uživatele. Na rozdíl od interaktivního přihlášení uživatelů tato přihlášení nevyžadují, aby uživatel poskytoval ověřovací faktor. Místo toho zařízení nebo klientská aplikace používají token nebo kód k ověření nebo přístupu k prostředku jménem uživatele. Obecně bude uživatel tyto přihlašování vnímat, jak se děje na pozadí aktivity uživatele.


**Velikost sestavy:** Celou <br>
**Příklady:** 

- Klientská aplikace používá k získání přístupového tokenu token OAuth 2,0 Refresh.

- Klient používá autorizační kód OAuth 2,0 k získání přístupového tokenu a aktualizačního tokenu.

- Uživatel provede jednotné přihlašování (SSO) k webu nebo aplikaci pro Windows na počítači připojeném k Azure AD.

- Uživatel se přihlásí k druhé aplikaci systém Microsoft Office, zatímco mají relaci na mobilním zařízení pomocí FOCI (rodina ID klientů).




Kromě výchozích polí zobrazuje sestava neinteraktivní přihlášení také následující: 

- ID prostředku

- Počet seskupených přihlášení




Nemůžete přizpůsobit pole zobrazená v této sestavě.


![Zakázané sloupce](./media/concept-all-sign-ins/disabled-columns.png "Zakázané sloupce")

Aby se usnadnilo shrnutí dat, jsou seskupeny neinteraktivní přihlašovací události. Klienti často vytvářejí mnoho neinteraktivních přihlášení jménem stejného uživatele v krátkém časovém období, které sdílí všechny stejné charakteristiky s výjimkou času, kdy došlo k pokusu o přihlášení. Klient může například získat přístupový token jednou za hodinu jménem uživatele. Pokud uživatel nebo klient nemění stav, IP adresa, prostředek a všechny další informace jsou pro každou žádost přístupového tokenu stejné. Když služba Azure AD zaznamená více přihlášení, která jsou identická než čas a datum, budou se tato přihlášení ze stejné entity agregovat do jednoho řádku. Řádek s více identickými přihlášeními (s výjimkou datum a čas vydání) bude mít hodnotu větší než 1 ve sloupci signed-ins. Rozbalením řádku můžete zobrazit všechna různá přihlášení a jejich různá časová razítka. Přihlášení se agreguje v neinteraktivních uživatelích, když se shodují následující data:


- Aplikace

- Uživatel

- IP adresa

- Status

- ID prostředku


Další možnosti:

- Rozbalením uzlu zobrazíte jednotlivé položky skupiny.  

- Kliknutím na jednotlivou položku zobrazíte všechny podrobnosti. 


![Podrobnosti přihlášení neinteraktivního uživatele](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Přihlašovací objekty instančního objektu

Na rozdíl od interaktivních a neinteraktivních přihlášení uživatelů nezahrnuje přihlášení instančního objektu uživatele. Místo toho se přihlásí libovolným neuživatelským účtem, jako jsou aplikace nebo instanční objekty (s výjimkou přihlašování spravované identity), které jsou zahrnuté jenom v sestavě spravované identity přihlášení. V těchto přihlášeních aplikace nebo služba poskytují vlastní přihlašovací údaje, jako je certifikát nebo tajný kód aplikace pro ověřování nebo přístup k prostředkům.


**Velikost sestavy:** Celou <br>
**Příklady:**

- Instanční objekt používá certifikát k ověření a přístup k Microsoft Graph. 

- Aplikace používá ke ověřování v toku přihlašovacích údajů klienta OAuth tajný klíč klienta. 


Tato sestava obsahuje výchozí zobrazení seznamu, které obsahuje:

- Datum přihlášení

- ID požadavku

- Název nebo ID objektu služby

- Status

- IP adresa

- Název prostředku

- ID prostředku

- Počet přihlášení

Nemůžete přizpůsobit pole zobrazená v této sestavě.

![Zakázané sloupce](./media/concept-all-sign-ins/disabled-columns.png "Zakázané sloupce")

Aby bylo snazší vyhlašovat data v protokolech přihlášení instančního objektu, jsou seskupeny události přihlášení instančního objektu. Přihlášení ze stejné entity za stejné podmínky se agreguje do jednoho řádku. Rozbalením řádku můžete zobrazit všechna různá přihlášení a jejich různá časová razítka. Přihlášení se agreguje v sestavě instančního objektu, když se shodují následující data:

- Název nebo ID objektu služby

- Status

- IP adresa

- Název nebo ID prostředku

Další možnosti:

- Rozbalením uzlu zobrazíte jednotlivé položky skupiny.  

- Klikněte na jednotlivou položku, aby se zobrazily všechny podrobnosti. 


![Podrobnosti sloupce](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Podrobnosti sloupce")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Spravovaná identita pro přihlašovací moduly prostředků Azure 

Spravovaná identita pro přihlašovací údaje prostředků Azure je přihlášení, které provedly prostředky, které mají své tajné kódy spravované v Azure, aby se zjednodušila Správa přihlašovacích údajů.

**Velikost sestavy:** Nejmenší <br> 
**4.6**

Virtuální počítač se spravovanými přihlašovacími údaji používá Azure AD k získání přístupového tokenu.   


Tato sestava obsahuje výchozí zobrazení seznamu, které obsahuje:


- ID spravované identity

- Název spravované identity

- Prostředek

- ID prostředku

- Počet seskupených přihlášení

Nemůžete přizpůsobit pole zobrazená v této sestavě.

Aby bylo snazší data vyhlašovat, spravované identity pro přihlašovací protokoly prostředků Azure, události neinteraktivního přihlašování se seskupují. Přihlášení ze stejné entity se agreguje do jednoho řádku. Rozbalením řádku můžete zobrazit všechna různá přihlášení a jejich různá časová razítka. Přihlášení se agreguje v sestavě spravované identity, když se shodují všechna následující data:

- Název nebo ID spravované identity

- Status

- IP adresa

- Název nebo ID prostředku

Vyberte položku v zobrazení seznamu, abyste zobrazili všechna přihlášení, která jsou seskupena pod uzlem.

Vyberte seskupenou položku, chcete-li zobrazit všechny podrobnosti o přihlášení. 


## <a name="sign-in-error-code"></a>Kód chyby přihlášení

Pokud se nezdařilo přihlášení, můžete získat další informace o příčině v části **základní informace** o související položce protokolu. 

![Snímek obrazovky ukazuje podrobné zobrazení informací.](./media/concept-all-sign-ins/error-code.png)
 
I když položka protokolu poskytuje důvod selhání, existují případy, kdy můžete získat další informace pomocí [Nástroje pro vyhledávání chyb při přihlašování](https://login.microsoftonline.com/error). Pokud je k dispozici například, tento nástroj poskytuje kroky odstranění problému.  

![Nástroj pro vyhledávání kódů chyb](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>Filtrování aktivit přihlašování

Nastavením filtru můžete zúžit rozsah vrácených přihlašovacích dat. Azure AD poskytuje širokou škálu dalších filtrů, které můžete nastavit. Při nastavování filtru byste měli vždy platit zvláštní pozornost nakonfigurovanému filtru rozsahu **dat** . Správný filtr rozsahu dat zajišťuje, že Azure AD vrátí jenom data, o která se opravdu zajímáte.     

Filtr rozsahu **data** umožňuje definovat časový rámec pro vracená data.
Možné hodnoty:

- Jeden měsíc

- Sedm dní

- Dvacet čtyři hodiny

- Vlastní

![Filtr rozsahu dat](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Filtrovat přihlášení uživatelů

Filtr pro interaktivní a neinteraktivní přihlášení je stejný. Z tohoto důvodu je filtr, který jste nakonfigurovali pro interaktivní přihlášení, trvalý pro neinteraktivní přihlášení a naopak. 






## <a name="access-the-new-sign-in-activity-reports"></a>Přístup k novým sestavám aktivit přihlašování 

Sestava aktivita přihlášení v Azure Portal poskytuje jednoduchou metodu pro přepínání a vypnutí sestavy náhledu. Pokud máte povolené sestavy Preview, získáte novou nabídku, která vám poskytne přístup ke všem typům sestav aktivit přihlašování.     


Přístup k novým sestavám přihlášení pomocí neinteraktivních a přihlašování aplikací: 

1. Na portálu [Azure Portal](https://portal.azure.com) vyberte **Azure Active Directory**.

    ![Vybrat Azure AD](./media/concept-all-sign-ins/azure-services.png)

2. V části **monitorování** klikněte na možnost **přihlášení**.

    ![Vybrat přihlášení](./media/concept-all-sign-ins/sign-ins.png)

3. Klikněte na panel **náhledu** .

    ![Povolit nové zobrazení](./media/concept-all-sign-ins/enable-new-preview.png)

4. Chcete-li přejít zpět k výchozímu zobrazení, klikněte znovu na panel **náhledu** . 

    ![Obnovit klasické zobrazení](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>Stažení sestav aktivit přihlašování

Když si stáhnete sestavu aktivity přihlašování, platí následující:

- Přihlašovací sestavu si můžete stáhnout jako soubor CSV nebo JSON.

- Můžete stáhnout až 100 záznamů. Pokud chcete stáhnout další data, použijte rozhraní API pro vytváření sestav.

- Váš soubor ke stažení je založený na výběru filtru, který jste provedli.

- Počet záznamů, které si můžete stáhnout, je omezený o [Azure Active Directory zásady uchovávání sestav](reference-reports-data-retention.md). 


![Stažení sestav](./media/concept-all-sign-ins/download-reports.png "Stažení sestav")


Každé stažení souboru CSV se skládá z šesti různých souborů:

- Interaktivní přihlášení

- Podrobnosti o ověřování interaktivních přihlášení

- Neinteraktivní přihlášení

- Podrobnosti ověření neinteraktivních přihlášení

- Přihlašovací objekty instančního objektu

- Spravovaná identita pro přihlašovací moduly prostředků Azure

Každé stažení JSON se skládá ze čtyř různých souborů:

- Interaktivní přihlášení (včetně podrobností ověření)

- Neinteraktivní přihlášení (včetně podrobností ověření)

- Přihlašovací objekty instančního objektu

- Spravovaná identita pro přihlašovací moduly prostředků Azure

![Stažení souborů](./media/concept-all-sign-ins/download-files.png "Stažení souborů")




## <a name="next-steps"></a>Další kroky

* [Kódy chyb sestav aktivit přihlašování](reference-sign-ins-error-codes.md)
* [Zásady uchovávání dat v Azure AD](reference-reports-data-retention.md)
* [Latence sestav Azure AD](reference-reports-latencies.md)
