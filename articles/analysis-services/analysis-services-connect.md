---
title: Připojení k serverům služby Azure Analysis Services | Dokumentace Microsoftu
description: Zjistěte, jak se připojit k a získání dat ze serveru služby Analysis Services v Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e2c60d48595f2c3687bd178e0f4eed010f14b568
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191526"
---
# <a name="connecting-to-servers"></a>Připojení k serverům

Tento článek popisuje připojení k serveru pomocí modelování dat a správu aplikací, jako je SQL Server Management Studio (SSMS) nebo SQL Server Data Tools (SSDT). Nebo s klientem vytváření sestav aplikace, jako je Microsoft Excel, Power BI Desktopu nebo vlastních aplikací. Připojení ke službě Azure Analysis Services používají protokol HTTPS.

## <a name="client-libraries"></a>Klientské knihovny

[Získání nejnovějších knihoven klienta](analysis-services-data-providers.md)

Všechna připojení k serveru, bez ohledu na typ, vyžadují aktualizovanou AMO, ADOMD.NET a OLEDB klientské knihovny pro připojení k a interface pomocí serveru služby Analysis Services. SSMS, rozšíření SSDT, Excelu 2016 nebo novější a Power BI klientské knihovny pro nejnovější instalaci nebo aktualizaci s měsíční verzemi. V některých případech je možné, že aplikace nemusí mít nejnovější verzi. Například když zpoždění zásady aktualizace nebo aktualizace Office 365 se na kanálu odloženo.

## <a name="server-name"></a>Název serveru

Když vytvoříte server služby Analysis Services v Azure, je třeba zadat jedinečný název a oblast, ve kterém má být vytvořen na serveru. Při zadávání názvu serveru v připojení, je schéma pojmenování serveru:

```
<protocol>://<region>/<servername>
```
 Kde je řetězec protokolu **asazure**, oblast je identifikátor Uri, ve kterém byla vytvořena na serveru (například westus.asazure.windows.net) a servername je název serveru jedinečný v rámci oblasti.

### <a name="get-the-server-name"></a>Získání názvu serveru

V **webu Azure portal** > server > **přehled** > **název serveru**, zkopírujte název celý server. Pokud se ostatní uživatelé ve vaší organizaci se připojují k tomuto serveru příliš, s nimi mohli sdílet tento název serveru. Při zadávání názvu serveru, musíte použít celou cestu.

![Získání názvu serveru v Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)


## <a name="connection-string"></a>Připojovací řetězec

Při připojování ke službě Azure Analysis Services pomocí tabulkový objektový Model, použijte následující připojovací řetězec formáty:

###### <a name="integrated-azure-active-directory-authentication"></a>Integrované ověřování Azure Active Directory

Integrované ověřování převezme mezipaměti přihlašovacích údajů Azure Active Directory, pokud je k dispozici. V opačném případě se zobrazí okno přihlášení k Azure.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Ověřování Azure Active Directory pomocí uživatelského jména a hesla

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Ověřování Windows (integrované zabezpečení)

Použijte účet Windows, který spouští aktuální proces.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Připojte se pomocí souboru .odc

Se staršími verzemi aplikace Excel můžou uživatelé připojit k serveru Azure Analysis Services pomocí souboru dat ODC (Office Connection). Další informace najdete v tématu [vytvořit soubor dat ODC (Office Connection)](analysis-services-odc.md).


## <a name="next-steps"></a>Další postup

[Propojení s Excelem](analysis-services-connect-excel.md)    
[Propojení s Power BI](analysis-services-connect-pbi.md)   
[Správa serveru](analysis-services-manage.md)   

