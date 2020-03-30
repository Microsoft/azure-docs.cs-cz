---
title: Připojení k serverům Služby Azure Analysis Services| Dokumenty společnosti Microsoft
description: Zjistěte, jak se připojit k serveru Analysis Services v Azure a získat je ze serveru Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3231b46060cbb755ada000473c8fbe873cc51ef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73147314"
---
# <a name="connecting-to-servers"></a>Připojení k serverům

Tento článek popisuje připojení k serveru pomocí aplikací pro modelování a správu dat, jako je SQL Server Management Studio (SSMS) nebo Visual Studio s projekty Analysis Services, nebo s klientskými aplikacemi pro vytváření sestav, jako je Microsoft Excel, Power BI Desktop, nebo vlastní aplikace. Připojení ke službě Azure Analysis Services používají protokol HTTPS.

## <a name="client-libraries"></a>Klientské knihovny

[Získejte nejnovější klientské knihovny](analysis-services-data-providers.md)

Všechna připojení k serveru, bez ohledu na typ, vyžadují aktualizované knihovny klientů AMO, ADOMD.NET a OLEDB pro připojení k serveru Analysis Services a připojení k němu. Pro SSMS, Visual Studio, Excel 2016 a novější a Power BI se nejnovější klientské knihovny nainstalované nebo aktualizované měsíčními verzemi. V některých případech je však možné, že aplikace nemusí mít nejnovější. Například když zásady zpoždění aktualizace nebo Aktualizace Office 365 jsou na odložené kanálu.

## <a name="server-name"></a>Název serveru

Při vytváření serveru Analysis Services v Azure zadáte jedinečný název a oblast, kde má být server vytvořen. Při zadávání názvu serveru v připojení je schéma pojmenování serveru:

```
<protocol>://<region>/<servername>
```
 Kde protokol je řetězec **asazure**, oblast je Uri, kde byl server vytvořen (například westus.asazure.windows.net) a název serveru je název jedinečného serveru v rámci oblasti.

### <a name="get-the-server-name"></a>Získání názvu serveru

V **webu Azure Portal** > název serveru > Název**serveru** **Přehled** > zkopírujte celý název serveru. Pokud se k tomuto serveru připojují i jiní uživatelé ve vaší organizaci, můžete s nimi sdílet tento název serveru. Při zadávání názvu serveru musí být použita celá cesta.

![Získání názvu serveru v Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Protokol pro region Východní USA 2 je **aspaaseastus2**.

## <a name="connection-string"></a>Připojovací řetězec

Při připojování ke službě Azure Analysis Services pomocí tabulkového objektového modelu použijte následující formáty připojovacího řetězce:

###### <a name="integrated-azure-active-directory-authentication"></a>Integrované ověřování služby Azure Active Directory

Integrované ověřování zachytí mezipaměť přihlašovacích údajů služby Azure Active Directory, pokud je k dispozici. Pokud ne, zobrazí se přihlašovací okno Azure.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Ověřování služby Azure Active Directory pomocí uživatelského jména a hesla

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Ověřování systému Windows (integrované zabezpečení)

Použijte účet systému Windows, ve který běží aktuální proces.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Připojení pomocí souboru ODC

Ve starších verzích Excelu se uživatelé můžou připojit k serveru Azure Analysis Services pomocí souboru datového připojení Office (.odc). Další informace najdete [v tématu Vytvoření souboru datového připojení sady Office (.odc).](analysis-services-odc.md)


## <a name="next-steps"></a>Další kroky

[Připojení k Excelu](analysis-services-connect-excel.md)    
[Připojení k Power BI](analysis-services-connect-pbi.md)   
[Správa serveru](analysis-services-manage.md)   

