---
title: Testovací verze – technická konfigurace, Microsoft Commercial Marketplace
description: Seznamte se s testovacími jednotkami. Testovací jednotky umožňují novým zákazníkům před potvrzením nákupu otestovat vaši nabídku.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/13/2019
author: keferna
ms.author: keferna
ms.openlocfilehash: 1772a61f45d88e0b63346c41d557cfddf35ab4a7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892617"
---
# <a name="test-drive-technical-configuration"></a>Technická konfigurace testovací verze

Možnost testovacího disku na komerčním tržišti společnosti Microsoft vám umožní nakonfigurovat praktickou a samoobslužnou prohlídku klíčových funkcí vašeho produktu. Pomocí testovacích jednotek si noví zákazníci můžou svou nabídku vyzkoušet ještě předtím, než ji nakupují. Další informace najdete v tématu [co je Test Drive?](what-is-test-drive.md) .

Pokud již nechcete pro vaši nabídku zadat testovací jednotku, vraťte se na stránku **nastavení nabídky** a zrušte zaškrtnutí políčka **Povolit testovací jednotku** . Ne všechny typy nabídek mají k dispozici testovací jednotku.

## <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager testovacích jednotek

Toto je jediná možnost testovacího disku pro virtuální počítač nebo nabídky aplikací Azure a vyžaduje poměrně podrobné nastavení. Přečtěte si níže uvedené části, kde [najdete podrobnosti předplatného nasazení](#deployment-subscription-details) a [seznam testovacích jednotek](#test-drive-listings), a pak pokračujte podle samostatného tématu [konfigurace Azure Resource Manager testovacích jednotek](azure-resource-manager-test-drive.md).

## <a name="hosted-test-drive"></a>Hostovaný testovací disk

Společnost Microsoft může odebrat složitost nastavení testovacích jednotek hostováním a údržbou zřizování a rušení zřizování služby. Konfigurace pro tento typ testovacích jednotek je stejná bez ohledu na to, jestli je testovací jednotka cílena na zákaznickou zapojení Dynamics 365 nebo cílovou skupinu operací Dynamics 365.

- **Maximální počet souběžných testovacích jednotek** (požadováno) – nastaví maximální počet zákazníků, kteří můžou použít testovací jednotku najednou. Každý souběžný uživatel bude používat licenci Dynamics 365, zatímco je testovací jednotka aktivní, takže je nutné mít k dispozici dostatek licencí, aby podporovaly maximální nastavený limit. Doporučená hodnota je 3-5.

- **Doba trvání testovacích jednotek** (požadováno) – zadejte počet hodin, po který bude testovací jednotka aktivní pro každého zákazníka. Po uplynutí této doby bude relace končit a už nebude využívat jednu z vašich licencí. V závislosti na složitosti vaší nabídky doporučujeme hodnotu 2-24 hodin. Tato doba může být nastavena pouze na celé hodiny (například "2 hodiny" je platná; "1,5 hodin" není). Uživatel může požádat o novou relaci, pokud dojde k nedostatku času a chcete znovu získat přístup k testovací jednotce.

- **Adresa URL instance** (požadováno) – adresa URL, na které zákazník zahájí svoji zkušební verzi. Obvykle adresa URL vaší instance Dynamics 365 spouští vaši aplikaci s nainstalovanou ukázkovou daty (například `https://testdrive.crm.dynamics.com` ).

- **Instance URL webového rozhraní API** (požadováno) – načtení adresy URL webového rozhraní API pro instanci Dynamics 365 přihlášením k účtu Microsoft 365 a přechodem na **Nastavení**  >  **přizpůsobení**  >  **Developer Resources**  >  **instance prostředků Web API (kořenová adresa URL služby)** , zkopírujte adresu URL, kterou tady najdete (například `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Název role** (povinné) – zadejte název role zabezpečení, kterou jste definovali ve vlastní testovací jednotce Dynamics 365, která se uživateli přiřadí během zkušební jednotky (například Test-Drive-role).

Chcete-li získat nápovědu k nastavení prostředí Dynamics 365 pro testovací verzi a udělení oprávnění AppSource ke zřízení a zrušení zřízení uživatelů testovacích jednotek ve vašem tenantovi, postupujte podle [těchto pokynů](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

## <a name="logic-app-test-drive"></a>Testovací jednotka aplikace logiky

Tento typ testovacích jednotek není hostitelem společnosti Microsoft. Použijte ho pro připojení k nabídce Dynamics 365 nebo k jinému vlastnímu prostředku, který zahrnuje celou řadu složitých architektur řešení. Další informace o nastavení testovacích jednotek aplikace logiky najdete v [provozu](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) na webu GitHub a na službě [Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) .

- **Oblast** (povinný, rozevírací seznam s jedním výběrem) – aktuálně jsou k dispozici 26 oblastí podporovaných v Azure, ve kterých se vaše testovací jednotka dá zpřístupnit. Prostředky vaší aplikace logiky se nasadí do vybrané oblasti. Pokud vaše aplikace logiky obsahuje nějaké vlastní prostředky uložené v konkrétní oblasti, ujistěte se, že je zde vybraná oblast. Nejlepším způsobem je plně nasadit vaši aplikaci logiky lokálně na vaše předplatné Azure na portálu a před provedením tohoto výběru ověřit, jestli funguje správně.

- **Maximální počet souběžných testovacích jednotek** (požadováno) – nastaví maximální počet zákazníků, kteří můžou použít testovací jednotku najednou. Tyto testovací jednotky jsou už nasazené a umožňují zákazníkům okamžitý přístup k nim bez čekání na nasazení.

- **Doba trvání testovacího disku** (požadováno) – zadejte dobu, po kterou bude testovací jednotka aktivní, v počtu hodin. Testovací jednotka se po skončení tohoto časového období ukončí automaticky.

- **Název skupiny prostředků Azure** (požadováno) – zadejte název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md#resource-groups), kde se vaše testovací jednotka aplikace logiky uloží.

- **Název aplikace logiky Azure** (povinné) – zadejte název aplikace logiky, která uživateli přiřadí testovací jednotku. Tato aplikace logiky se musí uložit do skupiny prostředků Azure výše.

- **Zrušit zřízení názvu aplikace logiky** (povinné) – zadejte název aplikace logiky, který po dokončení zákazníka dezřídí testovací jednotku. Tato aplikace logiky se musí uložit do skupiny prostředků Azure výše.

## <a name="power-bi-test-drive"></a>Power BI testovacích jednotek

Produkty, které chtějí předvést interaktivní Power BI vizuál, můžou pomocí vloženého odkazu sdílet vlastní řídicí panel jako svoji testovací jednotku, nevyžaduje se žádná další technická konfigurace. Vše, co potřebujete udělat, je nahrát vloženou adresu URL Power BI.

Další informace o nastavení Power BIch aplikací najdete v tématu [co jsou Power BI aplikace?](https://docs.microsoft.com/power-bi/service-template-apps-overview)

## <a name="deployment-subscription-details"></a>Podrobnosti předplatného nasazení

Abyste Microsoftu umožnili nasazení testovacích jednotek vaším jménem, vytvořte a poskytněte samostatné jedinečné předplatné Azure (nevyžaduje se Power BI testovacích jednotek).

- **ID předplatného Azure** (vyžadované pro Azure Resource Manager a Logic Apps) – zadejte ID předplatného, které vám umožní udělit přístup ke službám účtů Azure za účelem vytváření sestav a fakturace využívání prostředků. Doporučujeme zvážit [vytvoření samostatného předplatného Azure](../cost-management-billing/manage/create-subscription.md) , které se použije pro testovací jednotky, pokud ho ještě nemáte. ID vašeho předplatného Azure můžete najít tak, že se přihlásíte k [Azure Portal](https://portal.azure.com/) a přejdete na kartu **předplatná** v nabídce na levé straně. Výběrem karty se zobrazí vaše ID předplatného (například "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID tenanta Azure AD** (povinné) – zadejte [ID TENANTA](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)Azure Active Directory (AD). Chcete-li zjistit toto ID, přihlaste se k [Azure Portal](https://portal.azure.com/), v levé nabídce vyberte kartu Active Directory, vyberte možnost **vlastnosti** a vyhledejte číslo **ID adresáře** v seznamu (například 50c464d3-4930-494c-963c-1e951d15360e). ID tenanta vaší organizace můžete také vyhledat pomocí adresy vašeho názvu domény na adrese [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Název tenanta Azure AD** (vyžadováno pro dynamickou 365) – zadejte název Azure Active Directory (AD). Pokud chcete najít tento název, přihlaste se k [Azure Portal](https://portal.azure.com/)v pravém horním rohu bude název vašeho tenanta uvedený pod názvem vašeho účtu.

- **ID aplikace Azure AD** (požadováno) – zadejte [ID aplikace](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)Azure Active Directory (AD). Chcete-li zjistit toto ID, přihlaste se k [Azure Portal](https://portal.azure.com/), v levé nabídce vyberte kartu Active Directory, vyberte možnost **Registrace aplikací** a vyhledejte uvedené číslo **ID aplikace** (například `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Tajný kód klienta aplikace Azure AD** (povinné) – zadejte svůj [tajný kód klienta](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)aplikace Azure AD. Tuto hodnotu najdete tak, že se přihlásíte k [Azure Portal](https://portal.azure.com/). V nabídce vlevo vyberte kartu **Azure Active Directory** , vyberte **Registrace aplikací** a vyberte svou aplikaci Test Drive. V dalším kroku vyberte **certifikáty a tajné klíče** , vyberte **nový tajný klíč klienta** , zadejte popis, v části **vypršení platnosti** vyberte **nikdy** a pak zvolte **Přidat** . Nezapomeňte hodnotu zkopírovat. Před zkopírováním hodnoty nedělejte odejít ze stránky.

## <a name="test-drive-listings"></a>Výpisy testovacích jednotek

Možnost **výpisy testovacích jednotek** , která se nachází na kartě **testovací jednotka** v partnerském centru, zobrazuje jazyky (a trhy), kde je k dispozici vaše testovací jednotka. v současné době je v angličtině (USA) k dispozici jediné umístění. Kromě toho tato stránka zobrazuje stav seznamu pro konkrétní jazyk a datum a čas, kdy byl přidán. Pro každý jazyk nebo trh budete muset definovat podrobnosti testovacích jednotek (popis, uživatelská příručka, videa atd.).

- **Popis** (povinné): Popište si testovou jednotku, co se zobrazí, cíle pro uživatele, kteří mají experimentovat, funkce k prozkoumávání a jakékoli relevantní informace, které uživatelům pomůžou určit, jestli se má vaše nabídka získat. Do tohoto pole lze zadat až 3 000 znaků textu.

- **Informace o přístupu** (vyžadované pro Azure Resource Manager a logické testovací jednotky): Vysvětlete, co zákazník potřebuje pro přístup k této testovací jednotce a k používání této zkušební jednotky znát. Projděte si scénář pro použití vaší nabídky a přesně toho, co by měl zákazník znát pro přístup k funkcím v rámci testovacích jednotek. Do tohoto pole lze zadat až 10 000 znaků textu.

- **User Manual** (povinné): podrobný návod k vašemu prostředí Test Drive. Ruční příručka uživatele by měla pokrývat přesně to, co chcete zákazníkovi získat ze zkušební jednotky a sloužit jako odkaz na případné dotazy, které mohou mít. Soubor musí být ve formátu PDF a po nahrání má název (maximálně 255 znaků).

- **Videa: přidat videa** (volitelné): tady můžete odkazovat na videa hostovaná jinde (533 x 324 pixelů), aby si zákazník mohl zobrazit návod k informacím, který jim pomůže lépe porozumět testovacím jednotkám, včetně toho, jak úspěšně používat funkce vaší nabídky a pochopit scénáře, které zvýrazní jejich výhody.
  - **Název** (povinné)
  - **Adresa URL** (jenom YouTube nebo Vimeo; povinné)
  - **Miniatura** (533 x 324 pixelů) – obrázek musí být ve formátu PNG.

Pokud momentálně vytváříte testovací jednotku v partnerském centru, vyberte před pokračováním **Uložit koncept** .

## <a name="next-steps"></a>Další kroky

- [Osvědčené postupy testovacích jednotek](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Přehled](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)(PDF; Ujistěte se, že je blokování automaticky otevíraných oken vypnuté)
- [Aktualizace stávající nabídky na komerčním webu Marketplace](partner-center-portal/update-existing-offer.md)

## <a name="additional-resources"></a>Další zdroje

## <a name="feedback"></a>Váš názor

Odeslat a zobrazit názor na [Tento produkt](https://feedback.azure.com/forums/216369-azure-marketplace)
