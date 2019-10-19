---
title: Publikování nabídky spravovaných služeb pro Azure Marketplace
description: Naučte se publikovat nabídku spravované služby, která zákazníkům zařadí správu delegovaných prostředků Azure.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 10/17/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 10105d06e48a727e71ea5cb03f2ffceb589df50a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595264"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Publikování nabídky spravovaných služeb pro Azure Marketplace

V tomto článku se dozvíte, jak publikovat veřejnou nebo soukromou nabídku spravovaných služeb, která [Azure Marketplace](https://azuremarketplace.microsoft.com) pomocí [portál partnerů cloudu](https://cloudpartner.azure.com/)a umožňuje zákazníkům, kteří si nabídku koupí, připojit prostředky pro delegovaný prostředek Azure. správu.

> [!NOTE]
> Aby bylo možné vytvořit a publikovat tyto nabídky, musíte mít platný [účet v partnerském centru](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) . Pokud účet ještě nemáte, [proces registrace](https://aka.ms/joinmarketplace) vás provede kroky pro vytvoření účtu v partnerském centru a registraci v programu komerčního tržiště. Vaše ID Microsoft Partner Network (MPN) se [automaticky přidruží](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) k nabídkám, které publikujete, abyste mohli sledovat svůj dopad na zapojení zákazníků.
>
> Pokud nechcete publikovat nabídku Azure Marketplace, můžete zákazníky připojit ručně pomocí Azure Resource Manager šablon. Další informace najdete v tématu připojení [zákazníka k delegované správě prostředků Azure](onboard-customer.md).

Publikování nabídky spravovaných služeb je podobné jako publikování jakéhokoli jiného typu nabídky Azure Marketplace. Další informace o tomto procesu najdete v tématu [Příručka pro publikování Azure Marketplace a AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) a [Správa nabídek Azure a AppSource Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers). Měli byste si také projít [zásady certifikace komerčního tržiště](https://docs.microsoft.com/legal/marketplace/certification-policies), zejména část [spravované služby](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) .

Jakmile zákazník vaši nabídku přidá, bude moct delegovat jedno nebo několik konkrétních předplatných nebo skupin prostředků, které se pak budou připravovat [pro správu delegovaných prostředků Azure](#the-customer-onboarding-process). Všimněte si, že před započetím předplatného (nebo skupin prostředků v rámci předplatného) musí být odběr autorizovaný k registraci tím, že ručně zaregistruje poskytovatele prostředků **Microsoft. ManagedServices** .

> [!IMPORTANT]
> Každý plán v nabídce spravovaných služeb obsahuje oddíl s **podrobnostmi o manifestu** , ve kterém definujete entity Azure Active Directory (Azure AD) ve vašem tenantovi, které budou mít přístup k delegovaným skupinám prostředků a/nebo předplatným pro zákazníky, kteří Kupte si plán. Je důležité si uvědomit, že jakákoli skupina (nebo uživatel nebo instanční objekt), kterou tady zadáte, bude mít stejná oprávnění pro každého zákazníka, který plán koupí. Pokud chcete přiřadit různé skupiny pro práci s každým zákazníkem, budete muset publikovat samostatný soukromý plán, který je exkluzivní pro každého zákazníka.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Vytvořte nabídku v portál partnerů cloudu

1. Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V levém navigačním panelu vyberte možnost **Nová nabídka**a pak vyberte **spravované služby**.
3. Pro nabídku se zobrazí oddíl **editoru** se čtyřmi částmi, které se mají vyplnit **: nastavení nabídek**, **plány**, **tržiště**a **Podpora**. Přečtěte si, kde najdete pokyny k dokončení těchto oddílů.

## <a name="enter-offer-settings"></a>Zadat nastavení nabídky

V části **nastavení nabídky** zadejte následující:

|Pole  |Popis  |
|---------|---------|
|**ID nabídky**     | Jedinečný identifikátor vaší nabídky (v rámci vašeho profilu vydavatele). Toto ID může obsahovat jenom malé alfanumerické znaky, pomlčky a podtržítka, maximálně 50 znaků. Mějte na paměti, že ID nabídky může být pro zákazníky viditelné v místech, například v adresách URL produktů a v sestavách fakturace. Po publikování této nabídky tuto hodnotu nemůžete změnit.        |
|**ID vydavatele**     | ID vydavatele, které bude přidruženo k této nabídce Pokud máte více než jedno ID vydavatele, můžete vybrat ten, který chcete pro tuto nabídku použít.       |
|**Název**     | Název (až 50 znaků), který zákazníci uvidí pro vaši nabídku v Azure Marketplace a Azure Portal. Použijte rozpoznatelný název značky, který zákazníci považují – Pokud tuto nabídku povýšíte prostřednictvím vlastního webu, nezapomeňte použít přesně stejný název.        |

Po dokončení vyberte **Uložit**. Nyní jste připraveni přejít k části **plány** .

## <a name="create-plans"></a>Vytvoření plánů

Každá nabídka musí mít jeden nebo více plánů (někdy označovaných jako SKU). Můžete přidat více plánů pro podporu různých sad funkcí v různých cenách nebo upravit konkrétní plán pro omezené cílové skupiny konkrétních zákazníků. Zákazníci si můžou prohlédnout plány, které jsou pro ně k dispozici v rámci nadřízené nabídky.

V části plány pro každý plán, který chcete vytvořit, vyberte **Nový plán**. Pak zadejte **ID plánu**. Toto ID může obsahovat jenom malé alfanumerické znaky, pomlčky a podtržítka, maximálně 50 znaků. ID plánu může být pro zákazníky viditelné v místech, například v adresách URL produktů a v sestavách fakturace. Po publikování této nabídky tuto hodnotu nemůžete změnit.

Dále proveďte následující části v části **podrobnosti plánu** :

|Pole  |Popis  |
|---------|---------|
|**Název**     | Popisný název pro plán zobrazení Maximální délka 50 znaků.        |
|**Souhrn**     | Stručný popis plánu, který se má zobrazit pod nadpisem Maximální délka 100 znaků.        |
|**Popis**     | Popisný text, který poskytuje podrobnější vysvětlení plánu.         |
|**Model fakturace**     | Tady jsou uvedené 2 modely fakturace, ale musíte si vybrat **vlastní licenci** pro nabídky spravované služby. To znamená, že vaše zákazníky budete fakturovat přímo za náklady související s touto nabídkou a Microsoft vám nebude účtovat žádné poplatky.   |
|**Jedná se o soukromý plán?**     | Označuje, zda je SKU soukromá nebo veřejná. Výchozí **hodnota není (Public** ). Pokud necháte tento výběr, váš plán se nebude omezovat na konkrétní zákazníky (nebo na určitý počet zákazníků). Když publikujete veřejný plán, nemůžete ho později změnit na privátní. Pokud chcete, aby byl tento plán dostupný jenom pro konkrétní zákazníky, vyberte **Ano**. Když to uděláte, budete muset zákazníky identifikovat tak, že zadáte jejich ID předplatného. Můžete je zadat jednou (až pro 10 odběry) nebo nahráním souboru. CSV (až 20 000 odběrů). Nezapomeňte sem zahrnout vlastní odběry, abyste mohli otestovat a ověřit nabídku. Další informace najdete v tématu [soukromé SKU a plány](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  |

Nakonec dokončete část **Podrobnosti manifestu** . Tím se vytvoří manifest s autorizačními informacemi pro správu zákaznických prostředků. Zde uvedená informace je nutná k připojení zákazníků ke správě delegovaných prostředků Azure. Jak bylo uvedeno výše, tato oprávnění budou platit pro každého zákazníka, který plán koupí, takže pokud chcete omezit přístup na konkrétního zákazníka, budete muset publikovat privátní plán pro výhradní použití.

- Nejprve zadejte **verzi** manifestu. Použijte formát *n. n. n* (například 1.2.5).
- Potom zadejte **ID tenanta**. Toto je identifikátor GUID přidružený k Azure Active Directorymu ID tenanta vaší organizace (tj. tenant, ve kterém budete pracovat, abyste mohli spravovat prostředky vašich zákazníků). Pokud tyto možnosti nemáte k dispozici, můžete ji najít přesunutím ukazatele myši na název účtu v pravém horním rohu Azure Portal, nebo výběrem **přepínače Adresář**. 
- Nakonec do svého plánu přidejte jednu nebo více **autorizačních** položek. Autorizace definují entity, které mají přístup k prostředkům a předplatným pro zákazníky, kteří si plán kupují. Tyto informace musíte zadat, chcete-li získat přístup k prostředkům jménem zákazníka pomocí delegované správy prostředků Azure.
  Pro každou autorizaci zadejte následující. Pak můžete vybrat **nové autorizace** tolikrát, kolikrát je potřeba, a přidat tak další definice uživatelů nebo rolí.
  - **ID objektu Azure AD**: identifikátor Azure AD uživatele, skupiny uživatelů nebo aplikace, kterým budou udělena určitá oprávnění (jak je popsáno v definici role) s prostředky vašich zákazníků.
  - **Zobrazovaný název objektu Azure AD**: popisný název, který zákazníkovi pomůže pochopit účel této autorizace. Zákazník uvidí tento název při delegování prostředků.
  - **Definice role**: v seznamu vyberte jednu z dostupných předdefinovaných rolí Azure AD. Tato role určuje oprávnění, která bude mít uživatel v poli **ID objektu Azure AD** v materiálech vašich zákazníků. Informace o těchto rolích najdete v tématu [předdefinované role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  - **Přiřaditelné role**: to se vyžaduje jenom v případě, že jste pro tuto autorizaci vybrali správce přístupu uživatele v **definici role** . Pokud ano, musíte sem přidat jednu nebo více rolí, které lze přiřadit. Uživatel v poli **ID objektu Azure AD** bude moci přiřadit tyto **role** ke [spravovaným identitám](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Pro tohoto uživatele se nepoužijí žádná jiná oprávnění, která se běžně nevztahují k roli správce přístupu uživatele. Pokud tady nevyberete jednu nebo víc rolí, odeslání se nepředá. (Pokud jste nevybrali správce přístupu uživatele pro definici role tohoto uživatele, toto pole nemá žádný vliv.)

> [!TIP]
> Ve většině případů budete chtít přiřadit oprávnění k skupině uživatelů nebo instančnímu objektu služby Azure AD, a ne k řadě jednotlivých uživatelských účtů. To vám umožní přidat nebo odebrat přístup pro jednotlivé uživatele bez nutnosti aktualizace a opětovného publikování plánu, když se změní vaše požadavky na přístup.

Až skončíte s přidáváním plánů, vyberte **Uložit**a potom přejděte do části **Marketplace** .

## <a name="provide-marketplace-text-and-images"></a>Zadání textu a obrázků z Marketplace

V části **Marketplace** můžete zadat text a obrázky, které se zákazníkům zobrazí v Azure Marketplace a Azure Portal.

Poskytněte informace pro následující pole v části **Přehled** :

|Pole  |Popis  |
|---------|---------|
|**Název**     |  Název nabídky, často dlouhého a formálního názvu. Tento název se bude zobrazovat na webu Marketplace. Maximální délka 50 znaků. Ve většině případů by měl být stejný jako **název** , který jste zadali v části **nastavení nabídky** .       |
|**Souhrn**     | Stručný účel nebo funkce vaší nabídky Obvykle se zobrazuje pod nadpisem. Maximální délka 100 znaků.        |
|**Dlouhý souhrn**     | Delší souhrn účelu nebo funkce vaší nabídky. Maximální délka 256 znaků.        |
|**Popis**     | Další informace o vaší nabídce V tomto poli je maximální délka 3000 znaků a podporuje jednoduché formátování HTML. V popisu musíte uvést slova "spravovaná služba" nebo "spravované služby".       |
|**Identifikátor marketingu**     | Jedinečný identifikátor popisný z adresy URL. použije se v adresách URL Marketplace pro tuto nabídku. Pokud je například vaším vydavatelem ID *Contoso* a váš marketingový identifikátor je *dotazů*, adresa URL vaší nabídky v Azure Marketplace bude *https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp* .        |
|**Náhled ID předplatných**     | Přidejte jeden až 100 identifikátorů předplatného. Zákazníci, kteří jsou přidruženi k těmto předplatným, budou moci zobrazit nabídku v Azure Marketplace ještě předtím, než budou živá. Do této služby doporučujeme zahrnout vlastní odběry, abyste si mohli prohlédnout, jak se vaše nabídka zobrazí v Azure Marketplace před zpřístupněním zákazníkům.  (Podpora Microsoftu a technické týmy budou moci zobrazit vaši nabídku i v období Preview.)   |
|**Užitečné odkazy**     | Adresy URL související s vaší nabídkou, například dokumentace, poznámky k verzi, nejčastější dotazy atd.        |
|**Navrhované kategorie (max. 5)**     | Jedna nebo více kategorií (až pět), které se vztahují na vaši nabídku. Tyto kategorie usnadňují zákazníkům vyhledávání vaší nabídky v Azure Marketplace a Azure Portal.        |

V části **marketingové artefakty** můžete nahrát logo a další prostředky, které se mají zobrazit s vaší nabídkou. Volitelně můžete nahrávat snímky obrazovky nebo odkazy na videa, která zákazníkům pomůžou pochopit vaši nabídku.

Vyžadují se čtyři velikosti loga: **malá (40x40)** , **střední (90x90)** , **Velká (115x115)** a **roztažitelné (255x155)** . Postupujte podle těchto pokynů pro vaše loga:

- Design Azure má jednoduchou paletu barev. Omezte počet primárních a sekundárních barev ve vašem logu.
- Barvami motivů na portálu jsou bílá a černá. Nepoužívejte tyto barvy jako barvu pozadí pro vaše logo. Použijte barvu, která vaše logo na portálu zvýrazní. Doporučujeme použít jednoduché primární barvy.
- Pokud používáte průhledné pozadí, ujistěte se, že logo a text nejsou bílé, černé nebo modré.
- Logo by mělo působit plochým dojmem a nemělo by obsahovat barevné přechody. Nepoužívejte v logu pozadí s barevným přechodem.
- Na logo neumisťujte žádný text, a to ani název vaší společnosti nebo název značky.
- Zajistěte, aby logo nebylo roztažené.

Logo **Hero (815x290)** je volitelné, ale doporučuje se. Pokud zadáte logo Hero, postupujte podle těchto pokynů:

- Do loga Hero nezahrnujte text a nezapomeňte na pravé straně loga opustit 415 pixelů prázdného místa. To je nutné pro ponechání místa pro textové prvky, které budou vloženy programově: zobrazované jméno vydavatele, název plánu, nabídka dlouhého souhrnu.
- Pozadí loga Hero nesmí být černé, bílé nebo transparentní. Ujistěte se, že barva pozadí není příliš světlá, protože vložený text se zobrazí bíle.
- Po publikování nabídky pomocí ikony Hero ji nemůžete odebrat (i když ji můžete v případě potřeby aktualizovat jinou verzí).

V části **Správa zájemců** můžete vybrat systém CRM, ve kterém budou vaše potenciální zákazníci uložené. Všimněte si, že pro [zásady certifikace spravovaných služeb](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)se vyžaduje **cíl zájemce** .

Nakonec zadejte **adresu URL zásad ochrany osobních údajů** a **podmínky použití** v části **právní** . Můžete také zadat, jestli se má pro tuto nabídku používat [standardní smlouva](https://docs.microsoft.com/azure/marketplace/standard-contract) .

Nezapomeňte změny uložit před přechodem k části **Podpora** .

## <a name="add-support-info"></a>Přidat informace o podpoře

V části **Podpora** zadejte jméno, e-mail a telefonní číslo pro technický kontakt a kontakt na zákaznickou podporu. Budete taky muset poskytnout adresy URL podpory. Společnost Microsoft může tyto informace použít, když se budeme muset obrátit na firemní a podpůrné problémy.

Po přidání těchto informací vyberte **Uložit.**

## <a name="publish-your-offer"></a>Publikování nabídky

Až budete spokojeni se všemi informacemi, které jste zadali, je dalším krokem publikování nabídky Azure Marketplace. Kliknutím na tlačítko **publikovat** zahajte proces provádění vaší nabídky v reálném čase. Další informace o tomto procesu najdete v tématu [publikování Azure Marketplace a nabídky AppSource](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer).

## <a name="the-customer-onboarding-process"></a>Proces zprovoznění zákazníků

Když zákazník přidá vaši nabídku, bude moct [delegovat jedno nebo několik konkrétních předplatných nebo skupin prostředků](view-manage-service-providers.md#delegate-resources) , které se pak budou připravovat pro správu delegovaných prostředků Azure. Pokud zákazník nabídku přijal, ale ještě nedelegoval žádné prostředky, uvidí v **horní části stránky** poskytovatelé [**služeb**](view-manage-service-providers.md) v Azure Portal poznámku. Pokud uživatel v tenantovi zákazníka nemůže toto delegování provést, je to pravděpodobně proto, že nemá roli vlastníka pro předplatné. Pokud chcete najít uživatele, kteří můžou delegovat předplatné, může uživatel vybrat předplatné v Azure Portal, otevřít **řízení přístupu (IAM)** a [Zobrazit všechny uživatele s rolí vlastníka](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#view-roles-and-permissions).

Předtím, než bude možné připojit předplatné (nebo skupiny prostředků v rámci předplatného), musí být odběr autorizovaný k registraci tím, že ručně zaregistruje poskytovatele prostředků **Microsoft. ManagedServices** . Uživatel v tenantovi zákazníka s rolí přispěvatel nebo vlastník může postupovat podle kroků uvedených v části [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/resource-manager-supported-services.md).

Zákazník pak může potvrdit, že předplatné je připravené k registraci, jedním z následujících způsobů.

### <a name="azure-portal"></a>Portál Azure

1. V Azure Portal vyberte předplatné.
1. Vyberte **Poskytovatelé prostředků**.
1. Potvrďte, že se **Microsoft. ManagedServices** zobrazí jako **zaregistrované**.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

Výsledek by měl vypadat přibližně takto:

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show --namespace "Microsoft.ManagedServices" --output table
```

Výsledek by měl vypadat přibližně takto:

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [prostředích pro správu mezi klienty](../concepts/cross-tenant-management-experience.md).
- V **Azure Portal můžete** [Zobrazit a spravovat zákazníky](view-manage-customers.md) .
