---
title: Přidání podmíněného přístupu do toku uživatele v Azure AD B2C
description: Naučte se, jak přidat podmíněný přístup k Azure AD B2C toků uživatelů. Nakonfigurujte nastavení vícefaktorového ověřování (MFA) a zásady podmíněného přístupu ve vašich uživatelských tocích, abyste vynutili zásady a napravili rizikové přihlášení.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6325a890ea297a3aa2bdad76a1d95c10448a7b61
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033902"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Přidání podmíněného přístupu do toků uživatelů v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Podmíněný přístup se dá přidat k vašim uživatelským tokům v Azure Active Directory B2C (Azure AD B2C) nebo k vlastním zásadám pro správu rizikových přihlášení k vašim aplikacím. Podmíněný přístup Azure Active Directory (Azure AD) je nástroj používaný Azure AD B2C k spojování signálů, rozhodování a prosazování zásad organizace.

![Podmíněný tok přístupu](media/conditional-access-user-flow/conditional-access-flow.png)

Automatizace hodnocení rizik pomocí podmínek zásad znamená, že se okamžitě identifikují rizikové přihlašování a pak se opraví nebo zablokuje.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="service-overview"></a>Přehled služby

Azure AD B2C vyhodnotí každou událost přihlášení a zajistí, že před udělením přístupu uživatele budou splněné všechny požadavky zásad. V této fázi **hodnocení** vyhodnocuje služba podmíněného přístupu signály shromážděné detekcí rizik ochrany identity během přihlašování. Výsledkem tohoto procesu vyhodnocení je sada deklarací identity, které určují, jestli se má přihlášení udělit nebo zablokovat. Zásady Azure AD B2C používají tyto deklarace k provedení akce v toku uživatele, jako je blokování přístupu nebo nenáročného uživatele s konkrétní opravou, jako je vícefaktorové ověřování (MFA). Možnost blokovat přístup přepíše všechna ostatní nastavení.

::: zone pivot="b2c-custom-policy"
Následující příklad ukazuje technický profil podmíněného přístupu, který se používá k vyhodnocení hrozby přihlášení.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

Ve fázi **nápravy** , která následuje, je uživatel vyzván při ověřování MFA. Po dokončení Azure AD B2C informuje ochranu identity, že identifikovaná hrozba byla opravena a kterou metodu. V tomto příkladu Azure AD B2C signalizuje, že uživatel úspěšně dokončil službu Multi-Factor Authentication Challenge. 

::: zone pivot="b2c-custom-policy"

Následující příklad ukazuje technický profil podmíněného přístupu, který se používá k nápravě identifikované hrozby:

```XML
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

## <a name="components-of-the-solution"></a>Součásti řešení

Jedná se o komponenty, které umožňují podmíněný přístup v Azure AD B2C:

- **Tok uživatele** nebo **vlastní zásada** , která uživatele provede procesem přihlášení a registrace.
- **Zásady podmíněného přístupu** , které přinášejí signály dohromady pro rozhodování a vymáhání zásad organizace. Když se uživatel do vaší aplikace přihlásí pomocí zásady Azure AD B2C, použije zásady podmíněného přístupu signály Azure AD Identity Protection k identifikaci rizikových přihlášení a nabídne příslušné nápravné opatření.
- **Registrovaná aplikace** , která uživatele přesměruje na odpovídající tok uživatele Azure AD B2C nebo vlastní zásady.
- [Prohlížeè](https://www.torproject.org/download/) pro simulaci rizikového přihlášení

## <a name="service-limitations-and-considerations"></a>Omezení služby a požadavky

Při použití podmíněného přístupu Azure AD Vezměte v úvahu následující skutečnosti:

- Identity Protection je k dispozici pro místní i sociální identity, jako je Google nebo Facebook. Pro sociální identity je nutné ručně aktivovat podmíněný přístup. Zjišťování je omezené, protože přihlašovací údaje účtu sociálních služeb jsou spravované externím zprostředkovatelem identity.
- V Azure AD B2C tenantech jsou k dispozici pouze podmnožina zásad [podmíněného přístupu Azure AD](../active-directory/conditional-access/overview.md) .


## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="pricing-tier"></a>Cenová úroveň

Pro vytváření zásad rizikového přihlašování se vyžaduje Azure AD B2C **Premium P2** . Klienti **Premium P1** můžou vytvořit zásadu, která je založená na zásadách umístění, aplikace, uživatele nebo skupiny. Další informace najdete v tématu [Změna cenové úrovně Azure AD B2C](billing.md#change-your-azure-ad-pricing-tier) .

## <a name="prepare-your-azure-ad-b2c-tenant"></a>Příprava tenanta Azure AD B2C

Pokud chcete přidat zásadu podmíněného přístupu, zakažte výchozí hodnoty zabezpečení:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
3. V části **služby Azure** vyberte **Azure AD B2C**. Nebo pomocí vyhledávacího pole vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **vlastnosti** a pak vyberte **Spravovat výchozí nastavení zabezpečení**.

   ![Zakázat výchozí hodnoty zabezpečení](media/conditional-access-user-flow/disable-security-defaults.png)

5. V části **Povolit výchozí nastavení zabezpečení** vyberte **ne**.

   ![Nastavte přepínač Povolit výchozí hodnoty zabezpečení na ne.](media/conditional-access-user-flow/enable-security-defaults-toggle.png)

## <a name="add-a-conditional-access-policy"></a>Přidání zásady podmíněného přístupu

Zásada podmíněného přístupu je příkaz if-then pro přiřazení a řízení přístupu. Zásada podmíněného přístupu přináší signály dohromady, aby bylo možné rozhodovat a vyhovět zásadám organizace. Logický operátor mezi přiřazeními je *a*. Operátor v každém přiřazení je *nebo*.

![Přiřazení podmíněného přístupu](media/conditional-access-user-flow/conditional-access-assignments.png)

Postup přidání zásady podmíněného přístupu:

1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **zabezpečení** vyberte **podmíněný přístup (Preview)**. Otevře se stránka **zásady podmíněného přístupu** .
1. Vyberte **+ Nová zásada**.
1. Zadejte název zásady, jako je například *blokování rizikového přihlášení*.
1. V části **přiřazení** zvolte **Uživatelé a skupiny** a pak vyberte jednu z následujících podporovaných konfigurací:

    |Zařadit členy  |Licence | Poznámky  |
    |---------|---------|---------|
    |**Všichni uživatelé** | P1, P2 |Pokud se rozhodnete zahrnout **všechny uživatele**, bude tato zásada mít vliv na všechny vaše uživatele. Abyste se ujistili, že nechcete se uzamknout, vylučte účet správce výběrem možnosti **vyloučit**, výběrem možnosti **role adresáře** a výběrem možnosti **globální správce** v seznamu. Můžete také vybrat možnost **Uživatelé a skupiny** a potom v seznamu **Vybrat vyloučené uživatele** vybrat svůj účet.  | 
 
1. Vyberte **cloudové aplikace nebo akce** a pak **Vyberte aplikace**. Vyhledejte [aplikaci předávající strany](tutorial-register-applications.md).

1. Vyberte **podmínky** a pak vyberte z následujících podmínek. Vyberte například **riziko přihlášení** a **vysoké**, **střední** a **nízké** úrovně rizika.
    
    |Podmínka  |Licence  |Poznámky  |
    |---------|---------|---------|
    |**Riziko uživatele**|P2|Riziko uživatele představuje pravděpodobnost zneužití dané identity nebo účtu.|
    |**Riziko přihlášení**|P2|Riziko přihlášení představuje pravděpodobnost, že daný požadavek na ověření není autorizovaný vlastníkem identity.|
    |**Platformy zařízení**|Nepodporováno| Je charakterizován operačním systémem, který běží na zařízení. Další informace najdete v tématu [platformy zařízení](../active-directory/conditional-access/concept-conditional-access-conditions.md#device-platforms).|
    |**Umístění**|P1, P2|Pojmenovaná umístění mohou zahrnovat informace o veřejné síti IPv4, zemi nebo oblast nebo neznámé oblasti, které nejsou namapovány na konkrétní země nebo oblasti. Další informace najdete v tématu [umístění](../active-directory/conditional-access/concept-conditional-access-conditions.md#locations). |
 
1. V části **Ovládací prvky přístupu** zvolte **Udělení**. Pak vyberte, jestli se má blokovat nebo udělit přístup:
    
    |Možnost  |Licence |Poznámka  |
    |---------|---------|---------|
    |**Blokovat přístup**|P1, P2| Zabrání přístupu na základě podmínek uvedených v těchto zásadách podmíněného přístupu.|
    |**Udělení přístupu** s **vyžadováním vícefaktorového ověřování**|P1, P2|Na základě podmínek uvedených v těchto zásadách podmíněného přístupu musí uživatel projít Azure AD B2C Multi-Factor Authentication.|

1. V části **Povolit zásadu** vyberte jednu z následujících možností:
    
    |Možnost  |Licence |Poznámka  |
    |---------|---------|---------|
    |**Pouze sestava**|P1, P2| Sestava – umožňuje správcům vyhodnotit dopad zásad podmíněného přístupu ještě předtím, než je povolí ve svém prostředí. Doporučujeme, abyste kontrolovali zásady s tímto stavem a určili dopad na koncové uživatele bez nutnosti ověřování službou Multi-Factor Authentication nebo blokování uživatelů. Další informace najdete v tématu [Kontrola výsledků podmíněného přístupu v sestavě auditu](#review-conditional-access-outcomes-in-the-audit-report) .|
    | **Zapnuto**| P1, P2| Zásady přístupu se vyhodnotí a nevynutily. |
    | **Vypnuto** | P1, P2| Zásady přístupu nejsou aktivované a nemá žádný vliv na uživatele. |

1. Povolte zásady podmíněného přístupu, a to tak, že vyberete **vytvořit**.

## <a name="add-conditional-access-to-a-user-flow"></a>Přidání podmíněného přístupu do toku uživatele

Po přidání zásady podmíněného přístupu Azure AD povolte podmíněný přístup v toku uživatele nebo vlastní zásady. Pokud povolíte podmíněný přístup, nemusíte zadávat název zásady.

V každém okamžiku se může vztahovat na jednotlivé uživatele víc zásad podmíněného přístupu. V takovém případě má přednost zásada řízení přístupu nejpřísnějším zásadám. Pokud třeba jedna zásada vyžaduje vícefaktorové ověřování (MFA), zatímco ostatní blokují přístup, uživatel se zablokuje.

## <a name="enable-multi-factor-authentication-optional"></a>Povolit službu Multi-Factor Authentication (volitelné)

Při přidávání podmíněného přístupu do toku uživatele zvažte použití **vícefaktorového ověřování (MFA)**. Uživatelé můžou použít jednorázový kód prostřednictvím SMS nebo hlasu nebo jednorázové heslo e-mailem pro službu Multi-Factor Authentication. Nastavení MFA jsou nezávislá na nastavení podmíněného přístupu. Vícefaktorové ověřování můžete nastavit na **vždycky zapnuto** , aby MFA se vždycky vyžadovalo bez ohledu na nastavení podmíněného přístupu. Nebo můžete nastavit vícefaktorové ověřování na **podmíněné** , aby se MFA vyžadovalo jenom v případě, že to vyžaduje zásada podmíněného přístupu.

> [!IMPORTANT]
> Pokud vaše zásada podmíněného přístupu uděluje přístup s MFA, ale uživatel nezapsal telefonní číslo, může být uživatel zablokován.

::: zone pivot="b2c-user-flow"

Pokud chcete pro tok uživatelů Povolit podmíněný přístup, ujistěte se, že verze podporuje podmíněný přístup. Tyto verze uživatelských toků jsou označeny jako **Doporučené**.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

1. V části **služby Azure** vyberte **Azure AD B2C**. Nebo pomocí vyhledávacího pole vyhledejte a vyberte **Azure AD B2C**.

1. V části **zásady** vyberte **toky uživatelů**. Pak vyberte tok uživatele.

1. Vyberte **vlastnosti** a ujistěte se, že tok uživatele podporuje podmíněný přístup, a to tak, že vyhledá nastavení s názvem **podmíněný přístup**.
 
   ![Konfigurace vícefaktorového ověřování a podmíněného přístupu ve vlastnostech](media/conditional-access-user-flow/add-conditional-access.png)

1. V části **Multi-Factor Authentication** vyberte požadovanou **metodu MFA** a potom v části **vynucení vícefaktorového** ověřování vyberte **podmíněný (doporučeno)**.
 
1. V části **podmíněný přístup** zaškrtněte políčko **vyhovět zásadám podmíněného přístupu** .

1. Vyberte **Uložit**.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Přidání podmíněného přístupu k zásadám

1. Získejte příklad zásad podmíněného přístupu na [GitHubu](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access).
1. V každém souboru nahraďte řetězec `yourtenant` názvem vašeho tenanta Azure AD B2C. Například pokud je název vašeho tenanta B2C *contosob2c*, všechny instance `yourtenant.onmicrosoft.com` se stanou `contosob2c.onmicrosoft.com` .
1. Nahrajte soubory zásad.

## <a name="test-your-custom-policy"></a>Testování vlastních zásad

1. Vyberte `B2C_1A_signup_signin_with_ca` zásadu nebo `B2C_1A_signup_signin_with_ca_whatif` a otevřete její stránku Přehled. Pak vyberte **Spustit tok uživatele**. V části **aplikace** vyberte *WebApp1*. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Zkopírujte adresu URL v části **Spustit koncový bod toku uživatele**.

1. Chcete-li simulovat rizikové přihlašování, otevřete prohlížeč pro [mandát](https://www.torproject.org/download/) a použijte adresu URL, kterou jste zkopírovali v předchozím kroku, a přihlaste se k registrované aplikaci.

1. Na přihlašovací stránce zadejte požadované informace a pokuste se o přihlášení. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat. V tokenu Dekódovatelné v jwt.ms byste měli vidět, že přihlášení bylo blokované.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**. V části **aplikace** vyberte *WebApp1*. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .

1. Zkopírujte adresu URL v části **Spustit koncový bod toku uživatele**.

1. Chcete-li simulovat rizikové přihlašování, otevřete prohlížeč pro [mandát](https://www.torproject.org/download/) a použijte adresu URL, kterou jste zkopírovali v předchozím kroku, a přihlaste se k registrované aplikaci.

1. Na přihlašovací stránce zadejte požadované informace a pokuste se o přihlášení. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat. V tokenu Dekódovatelné v jwt.ms byste měli vidět, že přihlášení bylo blokované.

::: zone-end

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Kontrola výsledků podmíněného přístupu v sestavě auditu

Kontrola výsledku události podmíněného přístupu:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

3. V části **služby Azure** vyberte **Azure AD B2C**. Nebo pomocí vyhledávacího pole vyhledejte a vyberte **Azure AD B2C**.

4. V části **aktivity** vyberte **protokoly auditu**.

5. Filtrujte protokol auditu nastavením **kategorie** na **B2C** a nastavení **typu prostředku aktivity** na **IdentityProtection**. Pak vyberte **Použít**.

6. Zkontrolujte aktivitu auditu až po dobu posledních sedmi dnů. K dispozici jsou následující typy aktivit:

   - **Vyhodnotit zásady podmíněného přístupu**: Tato položka protokolu auditu indikuje, že při ověřování proběhlo vyhodnocení podmíněného přístupu.
   - **Opravit uživatele**: Tato položka znamená, že koncový uživatel splnil požadavky na udělení nebo požadavky zásad podmíněného přístupu. Tato aktivita byla hlášena rizikovým modulům, aby zmírnila (snížila riziko) uživatele.

7. Výběrem položky **vyhodnotit položku protokolu zásad podmíněného přístupu** v seznamu otevřete stránku **Podrobnosti o aktivitě: protokol auditu** , která zobrazuje identifikátory protokolů auditu spolu s těmito informacemi v části **Další podrobnosti** :

   - **ConditionalAccessResult**: Grant vyžadovaná vyhodnocením podmíněných zásad.
   - **AppliedPolicies**: seznam všech zásad podmíněného přístupu, ve kterých byly podmínky splněny, a zásady jsou zapnuté.
   - **ReportingPolicies**: seznam zásad podmíněného přístupu, které byly nastaveny na režim pouze pro sestavy a kde byly splněny podmínky.

## <a name="next-steps"></a>Další kroky

[Přizpůsobení uživatelského rozhraní v Azure AD B2C toku uživatele](customize-ui-with-html.md)
