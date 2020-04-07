---
title: Instalace místní brány dat
description: Než budete mít přístup k místním datům z Azure Logic Apps, stáhněte a nainstalujte místní datovou bránu.
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 12/05/2019
ms.openlocfilehash: f2f8b9f207993c49201d03d3d1fed3c5800e8780
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673816"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Instalace místní brány dat pro Azure Logic Apps

Než se budete moci [připojit k místním zdrojům dat z Azure Logic Apps](../logic-apps/logic-apps-gateway-connection.md), stáhněte a nainstalujte místní [datovou bránu](https://aka.ms/on-premises-data-gateway-installer) do místního počítače. Brána funguje jako most, který poskytuje rychlý přenos dat a šifrování mezi zdroji dat v místním prostředí a aplikacemi logiky. Stejnou instalaci brány můžete použít s dalšími cloudovými službami, jako jsou Power BI, Power Automate, Power Apps a Azure Analysis Services. Informace o tom, jak používat bránu s těmito službami, naleznete v těchto článcích:

* [Místní datová brána Microsoft Power Automate](/power-automate/gateway-reference)
* [Místní datová brána Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Místní datová brána Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Místní datová brána Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

Tento článek ukazuje, jak stáhnout, nainstalovat a nastavit místní bránu dat, abyste měli přístup k místním zdrojům dat z Azure Logic Apps. Další informace o fungování [brány dat](#gateway-cloud-service) najdete také dále v tomto tématu. Další informace o bráně najdete v tématu [Co je místní brána](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)? Chcete-li automatizovat úlohy instalace a správy brány, navštivte galerii Prostředí PowerShell pro [rutiny Prostředí PowerShell.](https://www.powershellgallery.com/packages/DataGateway/3000.15.15)

<a name="requirements"></a>

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Pokud nemáte účet Azure s předplatným, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

  * Váš účet Azure musí patřit do jednoho klienta nebo [adresáře Azure Active Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology) Pro instalaci a správu brány v místním počítači musíte použít stejný účet Azure.

  * Během instalace brány se přihlásíte pomocí svého účtu Azure, který propojí instalaci brány s vaším účtem Azure a jenom s tímto účtem. Později na webu Azure Portal musíte použít stejný účet Azure a tenanta Azure AD při vytváření prostředku brány Azure, který registruje a nárokuje instalaci vaší brány. V Aplikacích Logika Azure místní aktivační události a akce pak použít prostředek brány pro připojení k místním zdrojům dat.

    > [!NOTE]
    > Můžete propojit pouze jednu instalaci brány a jeden prostředek brány Azure k sobě navzájem. Nemůžete propojit stejnou instalaci brány s více účty Azure nebo prostředky brány Azure. Účet Azure však můžete propojit s více instalacemi brány a prostředky brány Azure. V místní aktivační události nebo akci můžete vybrat z různých předplatných Azure a pak vybrat přidružený prostředek brány.

  * Musíte se přihlásit pomocí pracovního účtu nebo školního účtu, označovaného také jako účet *organizace,* který vypadá jako `username@contoso.com`. Azure B2B (guest) účty ani osobní účty Microsoft, @hotmail.com @outlook.comjako jsou například nebo .

    > [!TIP]
    > Pokud jste si zaregistrovali nabídku Office 365 a nezadali jste `username@domain.onmicrosoft.com`pracovní e-mailovou adresu, může vaše adresa vypadat . Váš účet se ukládá v rámci tenanta ve službě Azure Active Directory (Azure AD). Ve většině případů je hlavní uživatelské jméno (UPN) pro váš účet Azure AD stejné jako vaše e-mailová adresa.
    >
    > Pokud chcete použít [předplatné Visual Studia Standard,](https://visualstudio.microsoft.com/vs/pricing/) které je propojené s účtem Microsoft, nejdřív [vytvořte tenanta ve službě Azure AD](../active-directory/develop/quickstart-create-new-tenant.md) nebo použijte výchozí adresář. Přidejte uživatele s heslem do adresáře a pak mu udělit přístup k předplatnému Azure. Během instalace brány se pak můžete přihlásit pomocí tohoto uživatelského jména a hesla.

* Zde jsou požadavky na místní počítač:

  **Minimální požadavky**

  *  .NET Framework 4.7.2
  * 64bitová verze systému Windows 7 nebo Windows Server 2008 R2 (nebo novější)

  **Doporučené požadavky**

  * 8jádrový procesor
  * 8 GB paměti
  * 64bitová verze systému Windows Server 2012 R2 nebo novější
  * Úložiště ssd jednotky (SSD) pro zařazování

  > [!NOTE]
  > Brána nepodporuje Windows Server Core.

* **Související úvahy**

  * Místní bránu dat instalujte pouze v místním počítači, nikoli v řadiči domény. Bránu nemusíte instalovat do stejného počítače jako zdroj dat. Pro všechny zdroje dat potřebujete pouze jednu bránu, takže bránu nemusíte instalovat pro každý zdroj dat.

    > [!TIP]
    > Chcete-li minimalizovat latenci, můžete nainstalovat bránu co nejblíže ke zdroji dat nebo do stejného počítače za předpokladu, že máte oprávnění.

  * Nainstalujte bránu do počítače, který je v kabelové síti, je připojený k internetu, vždy zapnutý a nepřejde do režimu spánku. V opačném případě se brána nemůže spustit a výkon může utrpět v bezdrátové síti.

  * Pokud chcete používat ověřování systému Windows, nainstalujte bránu do počítače, který je členem stejného prostředí služby Active Directory jako zdroje dat.

  * Oblast, kterou vyberete pro instalaci brány je stejné umístění, které je nutné vybrat při pozdějším vytvoření prostředku brány Azure pro aplikaci logiky. Ve výchozím nastavení je tato oblast ve stejném umístění jako váš klient Azure AD, který spravuje váš účet Azure. Během instalace brány však můžete změnit umístění.

  * Pokud aktualizujete instalaci brány na nejnovější verzi, odinstalujte nejprve aktuální bránu, abyste měli přehlednější zážitek.

  * Brána má dva režimy: standardní režim a osobní režim, který se vztahuje jenom na Power BI. Ve stejném počítači nelze spustit více bran ve stejném režimu.

  * Azure Logic Apps podporuje operace čtení a zápisu prostřednictvím brány. Tyto operace však mají [omezení velikosti datové části](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalace brány dat

1. [Stáhněte a spusťte instalační program brány v místním počítači](https://aka.ms/on-premises-data-gateway-installer).

1. Zkontrolujte minimální požadavky, zachovejte výchozí instalační cestu, přijměte podmínky použití a vyberte **instalovat**.

   ![Zkontrolujte požadavky a přijměte podmínky použití](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Až se brána úspěšně nainstaluje, zadejte e-mailovou adresu svého účtu Azure a pak **vyberte Přihlásit**se , například:

   ![Přihlášení pomocí pracovního nebo školního účtu](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Instalace brány může propojit jenom jeden účet Azure.

1. Vyberte **možnost Zaregistrovat novou bránu v tomto počítači** > **Další**. Tento krok zaregistruje instalaci brány pomocí [cloudové služby brány](#gateway-cloud-service).

   ![Registrace brány v místním počítači](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Zadejte tyto informace pro instalaci brány:

   * Název brány, který je jedinečný v rámci vašeho klienta Azure AD
   * Obnovovací klíč, který musí mít alespoň osm znaků, který chcete použít
   * Potvrzení obnovovacího klíče

   ![Poskytnutí informací pro instalaci brány](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Uložte a uchovávejte obnovovací klíč na bezpečném místě. Tento klíč potřebujete, pokud někdy budete chtít změnit umístění, přesunout, obnovit nebo převzít instalaci brány.

   Všimněte si **možnosti Přidat do existujícího clusteru bran**, který vyberete při instalaci dalších bran pro [scénáře s vysokou dostupností](#high-availability).

1. Podívejte se do oblasti pro cloudovou službu brány a [Azure Service Bus,](https://azure.microsoft.com/services/service-bus/) který používá instalace brány. Ve výchozím nastavení je tato oblast ve stejném umístění jako tenant Azure AD pro váš účet Azure.

   ![Potvrdit oblast pro službu brány a servisní sběrnici](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Chcete-li přijmout výchozí oblast, vyberte **konfigurovat**. Pokud však výchozí oblast není ta, která je nejblíže vám, můžete oblast změnit.

   *Proč měnit oblast pro instalaci brány?*

   Chcete-li například snížit latenci, můžete změnit oblast brány na stejnou oblast jako aplikace logiky. Nebo můžete vybrat oblast, která je nejblíže místnímu zdroji dat. Prostředek *brány v Azure* a aplikace logiky můžou mít různá umístění.

   1. Vedle aktuální oblasti vyberte **Změnit oblast**.

      ![Změna aktuální oblasti brány](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Na další stránce otevřete seznam **Vybrat oblast,** vyberte požadovanou oblast a vyberte **Hotovo**.

      ![Výběr jiné oblasti pro službu brány](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Zkontrolujte informace v okně konečného potvrzení. Tento příklad používá stejný účet pro Logic Apps, Power BI, Power Apps a Power Automate, takže brána je dostupná pro všechny tyto služby. Až budete připraveni, vyberte **Zavřít**.

   ![Potvrzení informací o bráně dat](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Teď [vytvořte prostředek Azure pro instalaci brány](../logic-apps/logic-apps-gateway-connection.md).

## <a name="check-or-adjust-communication-settings"></a>Kontrola nebo úprava nastavení komunikace

Místní brána dat závisí na [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) pro připojení ke cloudu a vytvoří odpovídající odchozí připojení k přidružené oblasti Azure brány. Pokud vaše pracovní prostředí vyžaduje, aby provoz prochází proxy nebo firewall pro přístup k Internetu, toto omezení může zabránit místní brány dat z připojení ke vzdálené službě brány a Azure Service Bus. Brána má několik nastavení komunikace, které můžete upravit. Další informace najdete v těchto tématech:

* [Úprava nastavení komunikace pro místní bránu dat](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [Konfigurace nastavení proxy serveru pro místní bránu dat](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Podpora vysoké dostupnosti

Chcete-li se vyhnout selhání jednotlivých bodů pro místní přístup k datům, můžete mít více instalací brány (pouze standardní režim) s každým v jiném počítači a nastavit je jako cluster nebo skupinu. Tímto způsobem, pokud primární brána není k dispozici, požadavky na data jsou směrovány do druhé brány a tak dále. Vzhledem k tomu, že do počítače můžete nainstalovat pouze jednu standardní bránu, je nutné nainstalovat každou další bránu, která je v clusteru, v jiném počítači. Všechny konektory, které pracují s místní bránou dat, podporují vysokou dostupnost.

* Už musíte mít alespoň jednu instalaci brány se stejným účtem Azure jako primární brána a obnovovací klíč pro tuto instalaci.

* Primární brána musí být spuštěna aktualizace brány od listopadu 2017 nebo novějšího.

Po nastavení primární brány při instalaci jiné brány vyberte **Přidat do existujícího clusteru brány**, vyberte primární bránu, která je první bránou, kterou jste nainstalovali, a zadejte obnovovací klíč pro tuto bránu. Další informace naleznete v [tématu Clustery s vysokou dostupností pro místní bránu dat](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Změna umístění, migrace, obnovení nebo převzetí existující brány

Pokud musíte změnit umístění brány, přesunout instalaci brány do nového počítače, obnovit poškozenou bránu nebo převzít vlastnictví existující brány, potřebujete obnovovací klíč, který byl poskytnut během instalace brány.

1. Spusťte instalační program brány v počítači, který má existující bránu. Pokud nemáte nejnovější instalační program brány, [stáhněte si nejnovější verzi brány](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Před obnovením brány v počítači, ve které je původní instalace brány, je nutné bránu v tomto počítači nejprve odinstalovat. Tato akce odpojí původní bránu.
   > Pokud odeberete nebo odstraníte cluster brány pro libovolnou cloudovou službu, nemůžete tento cluster obnovit.

1. Po otevření instalačního programu se přihlaste pomocí stejného účtu Azure, který byl použit k instalaci brány.

1. Vyberte **Migrovat, obnovit nebo převzít existující bránu** > **Další**, například:

   ![Vyberte možnost Migrace, obnovení nebo převzetí existující brány.](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Vyberte z dostupných clusterů a bran a zadejte obnovovací klíč pro vybranou bránu, například:

   ![Výběr brány a poskytnutí obnovovacího klíče](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Chcete-li oblast změnit, vyberte **možnost Změnit oblast**a vyberte novou oblast.

1. Až budete připraveni, vyberte **Konfigurovat,** abyste mohli úkol dokončit.

## <a name="tenant-level-administration"></a>Správa na úrovni tenanta

Chcete-li získat přehled o všech místních datových branách v tenantovi Azure AD, globální správci v tomto tenantovi se můžou přihlásit do [Centra správy Power Platform](https://powerplatform.microsoft.com) jako správce tenanta a vybrat možnost Brány **dat.** Další informace najdete v [tématu Správa na úrovni tenanta pro místní bránu dat](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Restartování brány

Ve výchozím nastavení je instalace brány v místním počítači spuštěna jako účet služby systému Windows s názvem "Místní služba brány dat". Instalace brány však `NT SERVICE\PBIEgwService` používá název pro pověření účtu "Přihlásit se jako" a má oprávnění "Přihlásit se jako služba".

> [!NOTE]
> Váš účet služby Windows se liší od účtu používaného pro připojení k místním zdrojům dat a od účtu Azure, který používáte při přihlášení ke cloudovým službám.

Stejně jako každá jiná služba systému Windows můžete bránu spustit a zastavit různými způsoby. Další informace naleznete [v tématu Restartování místní brány dat](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Jak brána funguje

Uživatelé ve vaší organizaci mají přístup k místním datům, ke kterým už mají autorizovaný přístup. Než se však tito uživatelé budou moci připojit k místnímu zdroji dat, musíte nainstalovat a nastavit místní bránu dat. Správce je obvykle osoba, která instaluje a nastaví bránu. Tyto akce mohou vyžadovat oprávnění správce serveru nebo zvláštní znalosti o místních serverech.

Brána pomáhá usnadnit rychlejší a bezpečnější komunikaci v zákulisí. Tato komunikace protéká mezi uživatelem v cloudu, cloudovou službou brány a místním zdrojem dat. Cloudová služba brány šifruje a ukládá vaše přihlašovací údaje ke zdroji dat a podrobnosti o bráně. Služba také směruje dotazy a jejich výsledky mezi uživatelem, bránou a místním zdrojem dat.

Brána pracuje s bránami firewall a používá pouze odchozí připojení. Veškerý provoz pochází jako zabezpečený odchozí provoz od agenta brány. Brána přenáší data z místních zdrojů na šifrovaných kanálech prostřednictvím [služby Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Tato sběrnice služby vytvoří kanál mezi bránou a volající službou, ale neukládá žádná data. Všechna data, která procházejí bránou, jsou šifrována.

![Architektura pro místní bránu dat](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> V závislosti na cloudové službě může být nutné nastavit zdroj dat pro bránu.

Tyto kroky popisují, co se stane, když pracujete s prvkem, který je připojený k místnímu zdroji dat:

1. Cloudová služba vytvoří dotaz spolu se šifrovanými přihlašovacími údaji pro zdroj dat. Služba pak odešle dotaz a pověření do fronty brány ke zpracování.

1. Cloudová služba brány analyzuje dotaz a odešle požadavek na Azure Service Bus.

1. Azure Service Bus odešle čekající požadavky do brány.

1. Brána získá dotaz, dešifruje přihlašovací údaje a připojí se k jednomu nebo více zdrojům dat s těmito pověřeními.

1. Brána odešle dotaz do zdroje dat pro spuštění.

1. Výsledky jsou odesílány ze zdroje dat zpět do brány a potom do cloudové služby brány. Cloudová služba brány pak používá výsledky.

### <a name="authentication-to-on-premises-data-sources"></a>Ověřování pro místní zdroje dat

Uložené přihlašovací údaje se používají k připojení z brány k místním zdrojům dat. Bez ohledu na uživatele brána používá uložené přihlašovací údaje pro připojení. Mohou existovat výjimky pro ověřování pro konkrétní služby, jako je DirectQuery a LiveConnect for Analysis Services v Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Cloudové služby Microsoftu používají [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) k ověřování uživatelů. Tenant Azure AD obsahuje uživatelská jména a skupiny zabezpečení. E-mailová adresa, kterou používáte pro přihlášení, je obvykle stejná jako hlavní uživatelské jméno (UPN) pro váš účet.

### <a name="what-is-my-upn"></a>Jaký je můj upn?

Pokud nejste správce domény, možná neznáte svůj hlavní název účtu. Chcete-li najít hlavní číslo účtu, spusťte `whoami /upn` příkaz z pracovní stanice. Přestože výsledek vypadá jako e-mailová adresa, výsledkem je hlavní název domény pro váš účet místní domény.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Synchronizace místní služby Active Directory s Azure AD

Hlavní název účtu pro místní účty Služby Active Directory a účty Azure AD musí být stejný. Takže se ujistěte, že každý místní účet služby Active Directory odpovídá vašemu účtu Azure AD. Cloudové služby vědí jenom o účtech v rámci Azure AD. Nemusíte tedy přidávat účet do místní služby Active Directory. Pokud účet ve službě Azure AD neexistuje, nemůžete tento účet použít.

Tady jsou způsoby, jak můžete shodovat s místními účty Služby Active Directory se službou Azure AD.

* Přidejte účty ručně do Azure AD.

  Vytvořte si účet na webu Azure Portal nebo v Centru pro správu Microsoftu 365. Ujistěte se, že název účtu odpovídá hlavní název účtu pro místní účet služby Active Directory.

* Synchronizujte místní účty s klientem Azure AD pomocí nástroje Azure Active Directory Connect.

  Nástroj Azure AD Connect poskytuje možnosti pro synchronizaci adresářů a nastavení ověřování. Mezi tyto možnosti patří synchronizace hash hesla, předávací ověřování a federace. Pokud nejste správce klienta nebo správce místní domény, obraťte se na správce IT a požádejte o nastavení Azure AD Connect. Azure AD Connect zajišťuje, že váš upn Azure AD odpovídá místní upn služby Active Directory. Tato shoda pomáhá, pokud používáte živé připojení Analysis Services s Power BI nebo funkce jednotného přihlašování (SSO).

  > [!NOTE]
  > Synchronizace účtů s nástrojem Azure AD Connect vytvoří nové účty ve vašem tenantovi Azure AD.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Nejčastější dotazy a řešení potíží

Další informace najdete v těchto tématech:

* [Nejčastější dotazy k místní datové bráně](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [Řešení problémů s místní bránou dat](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorování a optimalizace výkonu brány](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Další kroky

* [Připojení k místním datům z aplikací logiky](../logic-apps/logic-apps-gateway-connection.md)
* [Podnikové integrační funkce](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Konektory pro Azure Logic Apps](../connectors/apis-list.md)
