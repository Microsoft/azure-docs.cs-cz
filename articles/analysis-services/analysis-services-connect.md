---
title: Připojování k serverům Azure Analysis Services | Microsoft Docs
description: Naučte se, jak se připojit k serveru Analysis Services v Azure a získávat z něj data.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 71caad8ce650b86f4350b32974bb8d980538b223
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489013"
---
# <a name="connecting-to-servers"></a>Připojení k serverům

V tomto článku se dozvíte, jak se připojit k serveru pomocí aplikací pro modelování a správy dat, jako je SQL Server Management Studio (SSMS) nebo Visual Studio s Analysis Services projekty, nebo s aplikacemi klienta pro vytváření sestav, jako je Microsoft Excel, Power BI Desktop nebo vlastní aplikace. Připojení k Azure Analysis Services používají protokol HTTPS.

## <a name="client-libraries"></a>Klientské knihovny

[Získat nejnovější klientské knihovny](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current)

Všechna připojení k serveru bez ohledu na typ vyžadují aktualizované knihovny AMO, ADOMD.NET a klienta OLEDB pro připojení k serveru Analysis Services a jeho rozhraní. V případě aplikací SSMS, Visual Studio, Excel 2016 a novějších a Power BI jsou nejnovější klientské knihovny nainstalovány nebo aktualizovány pomocí měsíčních vydání. V některých případech je však možné, že aplikace nebude mít nejnovější verzi. Například při zpoždění aktualizace zásad nebo aktualizací Office 365 jsou na odloženém kanálu.

> [!NOTE]
> Klientské knihovny se nemohou připojit k Azure Analysis Services prostřednictvím proxy serverů, které vyžadují uživatelské jméno a heslo. 

## <a name="server-name"></a>Název serveru

Při vytváření Analysis Servicesho serveru v Azure zadáte jedinečný název a oblast, kde se má server vytvořit. Při zadávání názvu serveru v připojení je schéma pojmenovávání serverů:

```
<protocol>://<region>/<servername>
```
 Kde je protokol řetězec **asazure**, oblast je identifikátor URI, kde byl server vytvořen (například westus.asazure.Windows.NET) a servername je název vašeho jedinečného serveru v oblasti.

### <a name="get-the-server-name"></a>Získání názvu serveru

V **Azure Portal** > serveru > **Přehled**  >  **název serveru**zkopírujte celý název serveru. Pokud se k tomuto serveru připojí jiní uživatelé ve vaší organizaci, můžete tento název serveru sdílet s nimi. Při zadávání názvu serveru se musí použít celá cesta.

![Získání názvu serveru v Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Protokol pro Východní USA 2 oblast je **aspaaseastus2**.

## <a name="connection-string"></a>Připojovací řetězec

Při připojování k Azure Analysis Services pomocí tabulkového objektového modelu použijte následující formáty připojovacího řetězce:

###### <a name="integrated-azure-active-directory-authentication"></a>Integrované ověřování Azure Active Directory

Integrované ověřování vybírá mezipaměť přihlašovacích údajů Azure Active Directory, pokud je k dispozici. V takovém případě se zobrazí okno přihlášení do Azure.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Azure Active Directory ověřování s uživatelským jménem a heslem

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Ověřování systému Windows (integrované zabezpečení)

Použijte účet systému Windows, který spouští aktuální proces.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Připojení pomocí souboru ODC

Se staršími verzemi Excelu se uživatelé můžou k serveru Azure Analysis Services připojit pomocí souboru ODC (Office Data Connection). Další informace najdete v tématu [Vytvoření souboru ODC (Office Data Connection)](analysis-services-odc.md).

## <a name="connect-as-a-linked-server-from-sql-server"></a>Připojit jako propojený server z SQL Server

SQL Server se může připojit k prostředku Azure Analysis Services jako [propojený server](https://docs.microsoft.com/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine) zadáním MSOLAP jako zprostředkovatele zdrojů dat. Před konfigurací připojení připojeného serveru nezapomeňte nainstalovat nejnovější [klientskou knihovnu MSOLAP](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current) (poskytovatele). 

U připojení k serveru Azure Analysis Services musí být poskytovatel MSOLAP vytvořen mimo SQL Server proces. Při konfiguraci možností propojeného serveru se ujistěte, že **není vybraná**možnost **zakázat zpracování** .

Pokud je vybrána možnost **Povolení dezpracovávání** a instance poskytovatele je vytvořena v procesu SQL Server, vrátí se následující chyba:

```
OLE DB provider "MSOLAP" for linked server "(null)" returned message "The following system error occurred: ".

OLE DB provider "MSOLAP" for linked server "(null)" returned message "The connection failed because user credentials are needed and Sign-In UI is not allowed.".

Msg 7303, Level 16, State 1, Line 2
Cannot initialize the data source object of OLE DB provider "MSOLAP" for linked server "(null)".
```



## <a name="next-steps"></a>Další kroky

[Připojení pomocí Excelu](analysis-services-connect-excel.md)    
[Připojení pomocí Power BI](analysis-services-connect-pbi.md)   
[Správa serveru](analysis-services-manage.md)   

