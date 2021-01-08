---
title: Připojení ServiceNow ke konektoru pro správu služeb IT
description: Naučte se, jak připojit ServiceNow ke konektoru IT Service Management Connector (ITSMC) v Azure Monitor centrálně monitorovat a spravovat ITSM pracovní položky.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 7d1b4b3542f6914d413a5e29e57baa15e7a53346
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012780"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Připojení ServiceNow ke konektoru pro správu služeb IT

V tomto článku se dozvíte, jak nakonfigurovat připojení mezi instancí ServiceNow a konektorem Service Management Connector (ITSMC) v Log Analytics, abyste mohli centrálně spravovat pracovní položky pro správu IT služeb (ITSM).

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že pro připojení splňujete následující požadavky.

### <a name="itsmc-installation"></a>Instalace ITSMC

Informace o instalaci ITSMC najdete v tématu [Přidání řešení IT Service Management Connector](./itsmc-definition.md#add-it-service-management-connector).

> [!NOTE]
> ITSMC podporuje pouze oficiální nabídku software jako služby (SaaS) od ServiceNow. Privátní nasazení ServiceNow se nepodporují.

### <a name="oauth-setup"></a>Nastavení OAuth

Mezi podporované verze ServiceNow patří Orlandu, New York, Madrid, Londýn, Kingston, Jakarta, Istanbul, Helsinky a Ženeva.

Správci ServiceNow musí pro svou instanci ServiceNow vygenerovat ID klienta a tajný klíč klienta. V případě potřeby si přečtěte následující informace:

- [Nastavení OAuth pro Orlandu](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Nastavit OAuth pro New York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Nastavte OAuth pro Madrid.](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Nastavení OAuth pro Londýn](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Nastavení OAuth pro Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Nastavení OAuth pro Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Nastavte OAuth pro Istanbul.](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Nastavte OAuth pro Helsinky.](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Nastavte OAuth pro Ženeva.](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)

Jako součást nastavení OAuth doporučujeme:

1. [Vytvořte koncový bod pro klienty pro přístup k instanci](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html).

1. Aktualizujte životnost obnovovacího tokenu:

   1. V podokně **ServiceNow** vyhledejte **systém OAuth** a pak vyberte možnost **Registr aplikací**. 
   1. Vyberte název protokolu OAuth, který byl definován, a změňte **interval aktualizace na životnost** na **7 776 000 sekund** (90 dny). 
   1. Vyberte **Aktualizovat**. 

1. Vytvořte interní proceduru, abyste zajistili, že připojení zůstane aktivní. Několik dní do vypršení platnosti životnosti obnovovacího tokenu provede následující operace:

   1. [Dokončete proces ruční synchronizace pro konfiguraci konektoru ITSM](./itsmc-resync-servicenow.md).

   1. Odvolat starý obnovovací token Z bezpečnostních důvodů nedoporučujeme uchovávat staré klíče. 
   
      1. V podokně **ServiceNow** vyhledejte **systém OAuth** a pak vyberte **Spravovat tokeny**. 
      
      1. V seznamu vyberte starý token podle názvu OAuth a data vypršení platnosti.

         ![Snímek obrazovky, který zobrazuje seznam tokenů pro OAuth.](media/itsmc-connections/snow-system-oauth.png)
      1. Vyberte **odvolat přístup**  >  **odvolat**.

## <a name="install-the-user-app-and-create-the-user-role"></a>Instalace uživatelské aplikace a vytvoření role uživatele

Pomocí následujícího postupu nainstalujete aplikaci nyní pro uživatele a vytvoříte pro ni roli uživatele Integration. Tyto přihlašovací údaje použijete k vytvoření připojení ServiceNow v Azure.

> [!NOTE]
> ITSMC podporuje pouze oficiální uživatelskou aplikaci pro integraci služby Microsoft Log Analytics, která je stažena z úložiště ServiceNow. ITSMC nepodporuje žádné ingestování kódu na ServiceNow straně nebo v žádné aplikaci, která není součástí oficiálního řešení ServiceNow. 

1. Navštivte [ServiceNow Store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) a nainstalujte si **uživatelskou aplikaci pro ServiceNow a integraci Microsoft OMS** v instanci ServiceNow.
   
   >[!NOTE]
   >V rámci průběžného přechodu z Microsoft Operations Management Suite (OMS) na Azure Monitor se nyní používá OMS Log Analytics.     
2. Po instalaci přejdete na levý navigační panel instance ServiceNow a pak vyhledejte a vyberte **Microsoft OMS integrátor**.  
3. Vyberte **Kontrolní seznam instalace**.

   Stav se zobrazí jako  **nedokončený** , protože role uživatele ještě není vytvořená.

4. V textovém poli vedle pole **vytvořit uživatele integrace** zadejte jméno uživatele, který se může připojit k ITSMC v Azure.
5. Zadejte heslo pro tohoto uživatele a pak vyberte **OK**.  

Nově vytvořený uživatel se zobrazí s přiřazenými výchozími rolemi:

- personalize_choices
- import_transformer
- x_mioms_microsoft. User
- Standard
- template_editor
- view_changer

Po úspěšném vytvoření uživatele se stav **kontrolního seznamu kontroly instalace** přesune na **dokončeno** a zobrazí podrobnosti o roli uživatele vytvořenou pro danou aplikaci.

> [!NOTE]
> ITSMC může odesílat incidenty do ServiceNow bez dalších modulů nainstalovaných v instanci ServiceNow. Pokud používáte modul EventManagement v instanci ServiceNow a chcete vytvořit události nebo výstrahy v ServiceNow pomocí konektoru, přidejte do uživatele integrace následující role:
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>Vytvoření připojení
K vytvoření připojení ServiceNow použijte následující postup.

> [!NOTE]
> Výstrahy odesílané z Azure Monitor mohou vytvořit jeden z následujících prvků v ServiceNow: události, incidenty nebo výstrahy. 

1. V Azure Portal přejít na **všechny prostředky** a vyhledejte **partnera (YourWorkspaceName)**.

2. V části **zdroje dat pracovního prostoru** vyberte **připojení ITSM**.

   ![Snímek obrazovky, který zobrazuje výběr zdroje dat.](media/itsmc-connections/add-new-itsm-connection.png)

3. V horní části pravého podokna vyberte **Přidat**.

4. Zadejte informace, jak je popsáno v následující tabulce, a pak vyberte **OK**.

   | **Pole** | **Popis** |
   | --- | --- |
   | **Název připojení**   | Zadejte název instance ServiceNow, ke které se chcete připojit pomocí ITSMC. Tento název použijete později v Log Analytics při konfiguraci pracovních položek ITSM a zobrazení podrobných analýz. |
   | **Typ partnera**   | Vyberte **ServiceNow**. |
   | **Adresa URL serveru**   | Zadejte adresu URL instance ServiceNow, ke které se chcete připojit ITSMC. Adresa URL by měla ukazovat na podporovanou verzi SaaS s příponou *. ServiceNow.com*.|
   | **Uživatelské jméno**   | Zadejte uživatelské jméno integrace, které jste vytvořili v aplikaci ServiceNow, aby se podporovalo připojení k ITSMC.|
   | **Heslo**   | Zadejte heslo přidružené k tomuto uživatelskému jménu. **Poznámka**: uživatelské jméno a heslo se používají jenom k vytváření tokenů ověřování. Neukládají se kdekoli v rámci služby ITSMC.  |
   | **ID klienta**   | Zadejte ID klienta, které chcete použít pro OAuth2 ověřování, které jste vygenerovali dříve. Další informace o generování ID klienta a tajného klíče najdete v tématu [Nastavení OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
   | **Tajný kód klienta**   | Zadejte tajný klíč klienta generovaný pro toto ID.   |
   | **Rozsah synchronizace dat (ve dnech)** | Zadejte počet uplynulých dní, z nichž mají být data. Limit je 120 dní. |
   | **Pracovní položky, které se mají synchronizovat**   | Vyberte ServiceNow pracovní položky, které chcete synchronizovat s Azure Log Analytics prostřednictvím ITSMC. Vybrané hodnoty se importují do Log Analytics. Možnosti jsou incidenty a žádosti o změnu.|
   | **Vytvořit novou položku konfigurace v produktu ITSM** | Tuto možnost vyberte, pokud chcete vytvořit položky konfigurace v produktu ITSM. Když je tato možnost vybrána, ITSMC vytvoří v podporovaném systému ITSM položky konfigurace (Pokud žádné neexistují). Ve výchozím nastavení je zakázaný. |

![Snímek obrazovky s poli a možnostmi pro přidání připojení ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

Po úspěšném připojení a synchronizaci:

- Vybrané pracovní položky z instance ServiceNow jsou importovány do Log Analytics. Souhrn těchto pracovních položek můžete zobrazit na dlaždici **konektoru pro IT služby IT** .

- V této instanci ServiceNow můžete vytvářet incidenty ze Log Analytics výstrah nebo záznamů protokolu nebo z výstrah Azure.

> [!NOTE]
> ServiceNow má omezení přenosové rychlosti pro žádosti za hodinu. Pokud chcete nakonfigurovat limit, definujte **omezení míry příchozího REST API** v instanci ServiceNow.

## <a name="next-steps"></a>Další kroky

* [Přehled konektoru ITSM](itsmc-overview.md)
* [Vytváření pracovních položek ITSM z výstrah Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Řešení problémů v konektoru ITSM](./itsmc-resync-servicenow.md)