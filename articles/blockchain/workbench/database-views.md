---
title: Zobrazení databáze Azure Blockchain Workbench
description: Přehled dostupných zobrazení databáze Azure Blockchain Workbench Preview SQL DB.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 585084b4c85c48533bdad96d4f99813ef2e418b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325979"
---
# <a name="azure-blockchain-workbench-database-views"></a>Zobrazení databáze Azure Blockchain Workbench

Azure Blockchain Workbench Preview doručuje data z distribuovaných účetních knih do databáze SQL DB *mimo řetězec.* Databáze off-chain umožňuje používat SQL a stávající nástroje, jako je [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017), pro interakci s daty blockchain.

Azure Blockchain Workbench poskytuje sadu zobrazení databáze, které poskytují přístup k datům, které budou užitečné při provádění dotazů. Tato zobrazení jsou silně denormalized, aby bylo snadné rychle začít vytvářet sestavy, analýzy a jinak využívat blockchain data se stávajícími nástroji a bez nutnosti rekvalifikovat databázové pracovníky.

Tato část obsahuje přehled zobrazení databáze a data, která obsahují.

> [!NOTE]
> Jakékoli přímé použití databázových tabulek nalezených v databázi mimo tato zobrazení, i když je to možné, není podporováno.
>

## <a name="vwapplication"></a>vwAplikace

Toto zobrazení obsahuje podrobnosti o **aplikacích,** které byly odeslány do Azure Blockchain Workbench.

| Name (Název)                             | Typ          | Může být null | Popis                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Ne          | Jedinečný identifikátor aplikace |
| ApplicationName                  | nvarchar(50)  | Ne          | Název aplikace |
| Popis aplikace           | nvarchar(255) | Ano         | Popis žádosti |
| Název_applicationdisplayname           | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled               | bitové           | Ne          | Identifikuje, zda je aplikace aktuálně povolena.<br /> **Poznámka:** I když aplikace může být v databázi zakázána, přidružené smlouvy zůstávají na blockchainu a data o těchto smlouvách zůstávají v databázi. |
| UploadedDtTm                     | datetime2(7)  | Ne          | Datum a čas nahrání smlouvy |
| UploadedByUserId                 | int           | Ne          | ID uživatele, který aplikaci nahrál |
| UploadedByUserExternalId         | nvarchar(255) | Ne          | Externí identifikátor uživatele, který aplikaci nahrál. Ve výchozím nastavení je toto ID uživatelem ze služby Azure Active Directory pro konsorcium.                                                                                                |
| Stav uploadedByUserProvisioningStatus | int           | Ne          | Identifikuje aktuální stav procesu zřizování pro uživatele. Možné hodnoty: <br />0 – Uživatel byl vytvořen rozhraním API<br />1 – Klíč byl přidružen k uživateli v databázi<br />2 – Uživatel je plně zřízený                         |
| UploadedByUserFirstName          | nvarchar(50)  | Ano         | Křestní jméno uživatele, který smlouvu nahrál |
| Název UploadedByUserLastName           | nvarchar(50)  | Ano         | Příjmení uživatele, který smlouvu nahrál |
| Emailová adresa UploadedByUser       | nvarchar(255) | Ano         | E-mailová adresa uživatele, který smlouvu nahrál |

## <a name="vwapplicationrole"></a>vwApplicationRole

Toto zobrazení obsahuje podrobnosti o rolích, které byly definovány v aplikacích Azure Blockchain Workbench.

V aplikaci *převod majetku* mohou být například definovány role, jako jsou role *kupujícího* a *prodávajícího.*

| Name (Název)                   | Typ             | Může být null | Popis                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Ne          | Jedinečný identifikátor aplikace           |
| ApplicationName        | nvarchar(50)     | Ne          | Název aplikace                       |
| Popis aplikace | nvarchar(255)    | Ano         | Popis žádosti                  |
| Název_applicationdisplayname | nvarchar(255)    | Ne          | Název, který se má zobrazit v uživatelském rozhraní      |
| RoleId                 | int              | Ne          | Jedinečný identifikátor pro roli v aplikaci |
| RoleName (Název role)               | nvarchar50)      | Ne          | Název role                              |
| Popis role        | popis(255) | Ano         | Popis role                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Toto zobrazení obsahuje podrobnosti o rolích, které byly definovány v aplikacích Azure Blockchain Workbench a uživatelé s nimi spojené.

V aplikaci *převod majetku* může být například *Jan Novák* přidružen k roli *kupujícího.*

| Name (Název)                       | Typ          | Může být null | Popis                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Ne          | Jedinečný identifikátor aplikace                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | Ne          | Název aplikace                                                                                                                                                                                                           |
| Popis aplikace     | nvarchar(255) | Ano         | Popis žádosti                                                                                                                                                                                                      |
| Název_applicationdisplayname     | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Ne          | Jedinečný identifikátor pro roli v aplikaci                                                                                                                                                                                     |
| Název_applicationrole        | nvarchar50)   | Ne          | Název role                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | Ano         | Popis role                                                                                                                                                                                                             |
| UserId                     | int           | Ne          | ID uživatele přidruženého k roli |
| Externí id uživatele             | nvarchar(255) | Ne          | Externí identifikátor uživatele, který je přidružen k roli. Ve výchozím nastavení je toto ID uživatelem ze služby Azure Active Directory pro konsorcium.                                                                     |
| Stav userprovisioningu     | int           | Ne          | Identifikuje aktuální stav procesu zřizování pro uživatele. Možné hodnoty: <br />0 – Uživatel byl vytvořen rozhraním API<br />1 – Klíč byl přidružen k uživateli v databázi<br />2 – Uživatel je plně zřízený |
| Uživatelské jméno              | nvarchar(50)  | Ano         | Křestní jméno uživatele, který je přidružen k roli |
| UserLastName               | nvarchar(255) | Ano         | Příjmení uživatele, který je přidružen k roli |
| Adresa uživatelského e-mailu           | nvarchar(255) | Ano         | E-mailová adresa uživatele, který je přidružen k roli |

## <a name="vwconnectionuser"></a>uživatel vwConnectionUser

Toto zobrazení obsahuje podrobnosti o připojení definované v Azure Blockchain Workbench a uživatelé s nimi spojené. Pro každé připojení toto zobrazení obsahuje následující data:

-   Podrobnosti přidružené hlavní knihy
-   Přidružené informace o uživateli

| Name (Název)                     | Typ          | Může být null | Popis                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | Ne          | Jedinečný identifikátor pro připojení v Azure Blockchain Workbench |
| Adresa ConnectionEndpointUrl    | nvarchar(50)  | Ne          | Adresa URL koncového bodu pro připojení |
| ConnectionFundingAccount | nvarchar(255) | Ano         | Účet financování přidružený k připojení, je-li k dispozici |
| Id hlavní knihy                 | int           | Ne          | Jedinečný identifikátor hlavní knihy |
| Název hlavní knihy               | nvarchar(50)  | Ne          | Název hlavní knihy |
| Název zobrazení knihy        | nvarchar(255) | Ne          | Název hlavní knihy, která se má zobrazit v uživatelském okně |
| UserId                   | int           | Ne          | ID uživatele přidruženého k připojení |
| Externí id uživatele           | nvarchar(255) | Ne          | Externí identifikátor uživatele, který je přidružen k připojení. Ve výchozím nastavení je toto ID uživatelem ze služby Azure Active Directory pro konsorcium. |
| Stav userprovisioningu   | int           | Ne          |Identifikuje aktuální stav procesu zřizování pro uživatele. Možné hodnoty: <br />0 – Uživatel byl vytvořen rozhraním API<br />1 – Klíč byl přidružen k uživateli v databázi<br />2 – Uživatel je plně zřízený |
| Uživatelské jméno            | nvarchar(50)  | Ano         | Křestní jméno uživatele, který je přidružen k připojení |
| UserLastName             | nvarchar(255) | Ano         | Příjmení uživatele, který je přidružen k připojení |
| Adresa uživatelského e-mailu         | nvarchar(255) | Ano         | E-mailová adresa uživatele, který je přidružen k připojení |

## <a name="vwcontract"></a>vwSmlouva

Toto zobrazení obsahuje podrobnosti o nasazených kontraktech. Pro každou smlouvu obsahuje toto zobrazení následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Implementace přidružené hlavní knihy pro funkci
-   Podrobnosti o uživateli, který akci inicioval
-   Podrobnosti týkající se blockchainového bloku a transakce

| Name (Název)                                     | Typ           | Může být null | Popis                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | Ne          | Jedinečný identifikátor pro připojení v Azure Blockchain Workbench.                                                                                                                                                                                         |
| Adresa ConnectionEndpointUrl                    | nvarchar(50)   | Ne          | Adresa URL koncového bodu pro připojení |
| ConnectionFundingAccount                 | nvarchar(255)  | Ano         | Účet financování přidružený k připojení, je-li k dispozici |
| Id hlavní knihy                                 | int            | Ne          | Jedinečný identifikátor hlavní knihy |
| Název hlavní knihy                               | nvarchar(50)   | Ne          | Název hlavní knihy |
| Název zobrazení knihy                        | nvarchar(255)  | Ne          | Název hlavní knihy, která se má zobrazit v uživatelském okně |
| ApplicationId                            | int            | Ne          | Jedinečný identifikátor aplikace |
| ApplicationName                          | nvarchar (50)  | Ne          | Název aplikace |
| Název_applicationdisplayname                   | nvarchar (255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                       | bitové            | Ne          | Identifikuje, zda je aplikace aktuálně povolena.<br /> **Poznámka:** I když aplikace může být v databázi zakázána, přidružené smlouvy zůstávají na blockchainu a data o těchto smlouvách zůstávají v databázi.  |
| Id pracovního postupu                               | int            | Ne          | Jedinečný identifikátor pracovního postupu přidruženého ke smlouvě |
| Název pracovního postupu                             | nvarchar(50)   | Ne          | Název pracovního postupu přidruženého ke smlouvě |
| Název_pracovního postupuDisplayName                      | nvarchar(255)  | Ne          | Název pracovního postupu přidruženého ke smlouvě zobrazený v uživatelském rozhraní |
| Popis pracovního postupu                      | nvarchar(255)  | Ano         | Popis pracovního postupu přidruženého ke smlouvě |
| Id kódu smlouvy                           | int            | Ne          | Jedinečný identifikátor kódu smlouvy přidruženého ke smlouvě |
| Název_souboru_kontraktu                         | int            | Ne          | Název souboru obsahující inteligentní kód smlouvy pro tento pracovní postup. |
| KontraktUploadedDtTm                     | int            | Ne          | Datum a čas odeslání kódu smlouvy |
| ContractId                               | int            | Ne          | Jedinečný identifikátor smlouvy |
| Status zřizování smlouvy               | int            | Ne          | Identifikuje aktuální stav procesu zřizování smlouvy. Možné hodnoty: <br />0 – Smlouva byla vytvořena rozhraním API v databázi<br />1 – Smlouva byla odeslána do hlavní knihy<br />2 – Smlouva byla úspěšně nasazena do hlavní knihy<br />3 nebo 4 - Smlouva nebyla nasazena do hlavní knihy<br />5 - Smlouva byla úspěšně nasazena do hlavní knihy <br /><br />Počínaje verzí 1.5 jsou podporovány hodnoty 0 až 5. Pro zpětnou kompatibilitu v aktuální verzi je k dispozici zobrazení **vwContractV0,** které podporuje pouze hodnoty 0 až 2. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | E-mailová adresa uživatele, který smlouvu nasadil |
| ContractDeployedByUserId                 | int            | Ne          | Externí identifikátor pro uživatele, který nasadil smlouvu. Ve výchozím nastavení je toto ID identifikátorem identifikátor u uživatelů.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | Ne          | Externí identifikátor pro uživatele, který nasadil smlouvu. Ve výchozím nastavení je toto ID identifikátorem identifikátor u uživatelů.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Ne          | Identifikuje aktuální stav procesu zřizování pro uživatele. Možné hodnoty: <br />0 – uživatel byl vytvořen rozhraním API<br />1 – Klíč byl přidružen k uživateli v databázi <br />2 – Uživatel je plně zřízený                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | Ano         | Křestní jméno uživatele, který smlouvu nasadil |
| ContractDeployedByUserLastName           | nvarchar(255)  | Ano         | Příjmení uživatele, který smlouvu nasadil |
| Adresa EmailAddress uživatele MA       | nvarchar(255)  | Ano         | E-mailová adresa uživatele, který smlouvu nasadil |

## <a name="vwcontractaction"></a>vwContractAction

Toto zobrazení představuje většinu informací týkajících se opatření přijatých ve smlouvách a je navrženo tak, aby snadno usnadnilo běžné scénáře podávání zpráv. Pro každou akci, která byla provedena, obsahuje toto zobrazení následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Přidružená inteligentní smluvní funkce a definice parametrů
-   Implementace přidružené hlavní knihy pro funkci
-   Pro parametry jsou k dispozici specifické hodnoty instancí
-   Podrobnosti o uživateli, který akci inicioval
-   Podrobnosti týkající se blockchainového bloku a transakce

| Name (Název)                                     | Typ          | Může být null | Popis                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Ne          | Jedinečný identifikátor aplikace |
| ApplicationName                          | nvarchar(50)  | Ne          | Název aplikace |
| Název_applicationdisplayname                   | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                       | bitové           | Ne          | Toto pole identifikuje, zda je aplikace aktuálně povolena. Poznámka – I když aplikace může být v databázi zakázána, přidružené smlouvy zůstávají na blockchainu a data o těchto smlouvách zůstávají v databázi.                                                  |
| Id pracovního postupu                               | int           | Ne          | Jedinečný identifikátor pracovního postupu |
| Název pracovního postupu                             | nvarchar(50)  | Ne          | Název pracovního postupu |
| Název_pracovního postupuDisplayName                      | nvarchar(255) | Ne          | Název pracovního postupu, který se má zobrazit v uživatelském rozhraní |
| Popis pracovního postupu                      | nvarchar(255) | Ano         | Popis pracovního postupu |
| ContractId                               | int           | Ne          | Jedinečný identifikátor smlouvy |
| Status zřizování smlouvy               | int           | Ne          | Identifikuje aktuální stav procesu zřizování smlouvy. Možné hodnoty: <br />0 – Smlouva byla vytvořena rozhraním API v databázi<br />1 – Smlouva byla odeslána do hlavní knihy<br />2 – Smlouva byla úspěšně nasazena do hlavní knihy<br />3 nebo 4 - Smlouva nebyla nasazena do hlavní knihy<br />5 - Smlouva byla úspěšně nasazena do hlavní knihy <br /><br />Počínaje verzí 1.5 jsou podporovány hodnoty 0 až 5. Pro zpětnou kompatibilitu v aktuální verzi je k dispozici zobrazení **vwContractActionV0,** které podporuje pouze hodnoty 0 až 2. |
| Id kódu smlouvy                           | int           | Ne          | Jedinečný identifikátor pro implementaci kódu smlouvy |
| ContractLedgerIdentifier                 | nvarchar(255) | Ano         | Jedinečný identifikátor přidružený k nasazené verzi inteligentní smlouvy pro konkrétní distribuovanou hlavní knihu. Například ethereum. |
| ContractDeployedByUserId                 | int           | Ne          | Jedinečný identifikátor uživatele, který smlouvu nasadil |
| ContractDeployedByUserFirstName          | nvarchar(50)  | Ano         | Křestní jméno uživatele, který smlouvu nasadil |
| ContractDeployedByUserLastName           | nvarchar(255) | Ano         | Příjmení uživatele, který smlouvu nasadil |
| ContractDeployedByUserExternalId         | nvarchar(255) | Ne          | Externí identifikátor uživatele, který smlouvu nasadil. Ve výchozím nastavení je toto ID identifikátorem, který představuje jejich identitu v konsorciu Azure Active Directory.                                                                                                                                                |
| Adresa EmailAddress uživatele MA       | nvarchar(255) | Ano         | E-mailová adresa uživatele, který smlouvu nasadil |
| Pracovní postupFunctionId                       | int           | Ne          | Jedinečný identifikátor funkce pracovního postupu |
| Název funkce pracovního postupu                     | nvarchar(50)  | Ne          | Název funkce |
| Název_displeje WorkflowFunction              | nvarchar(255) | Ne          | Název funkce, která má být zobrazena v uživatelském rozhraní |
| WorkflowFunctionDescription              | nvarchar(255) | Ne          | Popis funkce |
| Smluvní actionid                         | int           | Ne          | Jedinečný identifikátor pro akci smlouvy |
| ContractActionProvisioningStatus         | int           | Ne          | Identifikuje aktuální stav procesu zřizování pro akci smlouvy. Možné hodnoty: <br />0 – Akce smlouvy byla vytvořena rozhraním API v databázi.<br />1 – Akce smlouvy byla odeslána do hlavní knihy.<br />2 – Akce smlouvy byla úspěšně nasazena do hlavní knihy.<br />3 nebo 4 - Smlouva nebyla nasazena do hlavní knihy<br />5 - Smlouva byla úspěšně nasazena do hlavní knihy <br /><br />Počínaje verzí 1.5 jsou podporovány hodnoty 0 až 5. Pro zpětnou kompatibilitu v aktuální verzi je k dispozici zobrazení **vwContractActionV0,** které podporuje pouze hodnoty 0 až 2. |
| ContractActionTimerazítko                  | datum (2,7) | Ne          | Časové razítko smluvní akce |
| Id kontraktuActionExecutedByUserId           | int           | Ne          | Jedinečný identifikátor uživatele, který provedl akci smlouvy |
| ContractActionExecutedByUserFirstName    | int           | Ano         | Jméno uživatele, který provedl akci smlouvy |
| KontraktActionExecutedByUserLastName     | nvarchar(50)  | Ano         | Příjmení uživatele, který provedl akci smlouvy |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | Ano         | Externí identifikátor uživatele, který provedl akci smlouvy. Ve výchozím nastavení je toto ID identifikátorem, který představuje jejich identitu v konsorciu Azure Active Directory. |
| Adresa emailu smluvní akceExecutedByUserEmailAddress | nvarchar(255) | Ano         | E-mailová adresa uživatele, který provedl akci smlouvy |
| WorkflowFunctionID              | int           | Ne          | Jedinečný identifikátor parametru funkce |
| Název_parametru WorkflowFunctionFunctionName            | nvarchar(50)  | Ne          | Název parametru funkce |
| Název_displeje WorkflowFunctionFunctionParameterName     | nvarchar(255) | Ne          | Název parametru funkce, který má být zobrazen v uživatelském rozhraní |
| WorkflowFunctionFunctionParametrId      | int           | Ne          | Jedinečný identifikátor datového typu přidruženého k parametru funkce pracovního postupu |
| WorkflowParameterNázev_typu__            | nvarchar(50)  | Ne          | Název datového typu přidruženého k parametru funkce pracovního postupu |
| Hodnota_parametru ContractAction_ParameterValue             | nvarchar(255) | Ne          | Hodnota parametru uloženého ve smlouvě smart |
| BlokHash                                | nvarchar(255) | Ano         | Hash bloku |
| Číslo bloku                              | int           | Ano         | Číslo bloku v hlavní knize |
| BlokTimerazítk                           | datum (2,7) | Ano         | Časové razítko bloku |
| Id transakce                            | int           | Ne          | Jedinečný identifikátor transakce |
| Transakcez                          | nvarchar(255) | Ano         | Strana, která transakci vytvořila |
| TransakceChcete-li                            | nvarchar(255) | Ano         | Strana, která byla sjednána s |
| Transakční hash                          | nvarchar(255) | Ano         | Hodnota hash transakce |
| TransactionIsWorkbenchTransakce        | bitové           | Ano         | Bit, který identifikuje, jestli je transakce transakce Azure Blockchain Workbench transakce |
| Stav zalesnění transakce            | int           | Ano         | Identifikuje aktuální stav procesu zřizování pro transakci. Možné hodnoty: <br />0 – Transakce byla vytvořena rozhraním API v databázi.<br />1 – Transakce byla odeslána do hlavní knihy.<br />2 – Transakce byla úspěšně nasazena do hlavní knihy.                 |
| TransactionValue                         | desetinné místo (32,2) | Ano         | Hodnota transakce |

## <a name="vwcontractproperty"></a>vwContractProperty

Toto zobrazení představuje většinu informací týkajících se vlastností spojených se smlouvou a je navrženo tak, aby snadno usnadnilo běžné scénáře vykazování. Pro každou přijatou vlastnost obsahuje toto zobrazení následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Podrobnosti o uživateli, který nasadil pracovní postup
-   Přidružená definice vlastností inteligentní smlouvy
-   Specifické hodnoty instance pro vlastnosti
-   Podrobnosti o státním majetku smlouvy

| Name (Název)                               | Typ          | Může být null | Popis                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Ne          | Jedinečný identifikátor aplikace |
| ApplicationName                    | nvarchar(50)  | Ne          | Název aplikace |
| Název_applicationdisplayname             | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                 | bitové           | Ne          | Identifikuje, zda je aplikace aktuálně povolena.<br />**Poznámka:** I když aplikace může být v databázi zakázána, přidružené smlouvy zůstávají na blockchainu a data o těchto smlouvách zůstávají v databázi.                      |
| Id pracovního postupu                         | int           | Ne          | Jedinečný identifikátor pracovního postupu |
| Název pracovního postupu                       | nvarchar(50)  | Ne          | Název pracovního postupu |
| Název_pracovního postupuDisplayName                | nvarchar(255) | Ne          | Název pracovního postupu zobrazený v uživatelském rozhraní |
| Popis pracovního postupu                | nvarchar(255) | Ano         | Popis pracovního postupu |
| ContractId                         | int           | Ne          | Jedinečný identifikátor smlouvy |
| Status zřizování smlouvy         | int           | Ne          | Identifikuje aktuální stav procesu zřizování smlouvy. Možné hodnoty: <br />0 – Smlouva byla vytvořena rozhraním API v databázi<br />1 – Smlouva byla odeslána do hlavní knihy<br />2 – Smlouva byla úspěšně nasazena do hlavní knihy<br />3 nebo 4 - Smlouva nebyla nasazena do hlavní knihy<br />5 - Smlouva byla úspěšně nasazena do hlavní knihy <br /><br />Počínaje verzí 1.5 jsou podporovány hodnoty 0 až 5. Pro zpětnou kompatibilitu v aktuální verzi je k dispozici zobrazení **vwContractPropertyV0,** které podporuje pouze hodnoty 0 až 2. |
| Id kódu smlouvy                     | int           | Ne          | Jedinečný identifikátor pro implementaci kódu smlouvy |
| ContractLedgerIdentifier           | nvarchar(255) | Ano         | Jedinečný identifikátor přidružený k nasazené verzi inteligentní smlouvy pro konkrétní distribuovanou hlavní knihu. Například ethereum. |
| ContractDeployedByUserId           | int           | Ne          | Jedinečný identifikátor uživatele, který smlouvu nasadil |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Ano         | Křestní jméno uživatele, který smlouvu nasadil |
| ContractDeployedByUserLastName     | nvarchar(255) | Ano         | Příjmení uživatele, který smlouvu nasadil |
| ContractDeployedByUserExternalId   | nvarchar(255) | Ne          | Externí identifikátor uživatele, který smlouvu nasadil. Ve výchozím nastavení je toto ID identifikátorem, který představuje jejich identitu v konsorciu Azure Active Directory. |
| Adresa EmailAddress uživatele MA | nvarchar(255) | Ano         | E-mailová adresa uživatele, který smlouvu nasadil |
| Id vlastnosti pracovního postupu                 | int           |             | Jedinečný identifikátor vlastnosti pracovního postupu |
| WorkflowPropertyDataTypeId         | int           | Ne          | ID datového typu vlastnosti |
| WorkflowPropertyNázev_name       | nvarchar(50)  | Ne          | Název datového typu vlastnosti |
| Název_pracovního postupu               | nvarchar(50)  | Ne          | Název vlastnosti pracovního postupu |
| Název_displeje_pracovního postupu        | nvarchar(255) | Ne          | Zobrazovaný název vlastnosti pracovního postupu |
| WorkflowPropertyDescription        | nvarchar(255) | Ano         | Popis nemovitosti |
| Hodnota PropertyValue              | nvarchar(255) | Ne          | Hodnota nemovitosti na smlouvě |
| Název státu                          | nvarchar(50)  | Ano         | Pokud tato vlastnost obsahuje stav smlouvy, je zobrazovaný název pro stát. Pokud není přidružen ke stavu, bude hodnota null. |
| Název_zobrazení stavu                   | nvarchar(255) | Ne          | Pokud tato vlastnost obsahuje stav, je zobrazovaný název pro stav. Pokud není přidružen ke stavu, bude hodnota null. |
| Hodnota stavu                         | nvarchar(255) | Ano         | Pokud tato vlastnost obsahuje stav, je hodnota stavu. Pokud není přidružen ke stavu, bude hodnota null. |

## <a name="vwcontractstate"></a>vwContractState

Toto zobrazení představuje většinu informací týkajících se stavu konkrétní smlouvy a je navrženo tak, aby snadno usnadnilo běžné scénáře vykazování. Každý záznam v tomto zobrazení obsahuje následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Podrobnosti o uživateli, který nasadil pracovní postup
-   Přidružená definice vlastností inteligentní smlouvy
-   Podrobnosti o státním majetku smlouvy

| Name (Název)                               | Typ          | Může být null | Popis                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Ne          | Jedinečný identifikátor aplikace |
| ApplicationName                    | nvarchar(50)  | Ne          | Název aplikace |
| Název_applicationdisplayname             | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                 | bitové           | Ne          | Identifikuje, zda je aplikace aktuálně povolena.<br />**Poznámka:** I když aplikace může být v databázi zakázána, přidružené smlouvy zůstávají na blockchainu a data o těchto smlouvách zůstávají v databázi. |
| Id pracovního postupu                         | int           | Ne          | Jedinečný identifikátor pracovního postupu |
| Název pracovního postupu                       | nvarchar(50)  | Ne          | Název pracovního postupu |
| Název_pracovního postupuDisplayName                | nvarchar(255) | Ne          | Název zobrazený v uživatelském rozhraní |
| Popis pracovního postupu                | nvarchar(255) | Ano         | Popis pracovního postupu |
| Id implementace účetní knihy Smlouvy     | nvarchar(255) | Ano         | Jedinečný identifikátor přidružený k nasazené verzi inteligentní smlouvy pro konkrétní distribuovanou hlavní knihu. Například ethereum. |
| ContractId                         | int           | Ne          | Jedinečný identifikátor smlouvy |
| Status zřizování smlouvy         | int           | Ne          |Identifikuje aktuální stav procesu zřizování smlouvy. Možné hodnoty: <br />0 – Smlouva byla vytvořena rozhraním API v databázi<br />1 – Smlouva byla odeslána do hlavní knihy<br />2 – Smlouva byla úspěšně nasazena do hlavní knihy<br />3 nebo 4 - Smlouva nebyla nasazena do hlavní knihy<br />5 - Smlouva byla úspěšně nasazena do hlavní knihy <br /><br />Počínaje verzí 1.5 jsou podporovány hodnoty 0 až 5. Pro zpětnou kompatibilitu v aktuální verzi je k dispozici zobrazení **vwContractStateV0,** které podporuje pouze hodnoty 0 až 2. |
| ConnectionId                       | int           | Ne          | Jedinečný identifikátor instance blockchain, do které je pracovní postup nasazen |
| Id kódu smlouvy                     | int           | Ne          | Jedinečný identifikátor pro implementaci kódu smlouvy |
| ContractDeployedByUserId           | int           | Ne          | Jedinečný identifikátor uživatele, který smlouvu nasadil |
| ContractDeployedByUserExternalId   | nvarchar(255) | Ne          | Externí identifikátor uživatele, který smlouvu nasadil. Ve výchozím nastavení je toto ID identifikátorem, který představuje jejich identitu v konsorciu Azure Active Directory. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Ano         | Křestní jméno uživatele, který smlouvu nasadil |
| ContractDeployedByUserLastName     | nvarchar(255) | Ano         | Příjmení uživatele, který smlouvu nasadil |
| Adresa EmailAddress uživatele MA | nvarchar(255) | Ano         | E-mailová adresa uživatele, který smlouvu nasadil |
| Id vlastnosti pracovního postupu                 | int           | Ne          | Jedinečný identifikátor vlastnosti pracovního postupu |
| WorkflowPropertyDataTypeId         | int           | Ne          | ID datového typu vlastnosti pracovního postupu |
| WorkflowPropertyNázev_name       | nvarchar(50)  | Ne          | Název datového typu vlastnosti pracovního postupu |
| Název_pracovního postupu               | nvarchar(50)  | Ne          | Název vlastnosti pracovního postupu |
| Název_displeje_pracovního postupu        | nvarchar(255) | Ne          | Zobrazovaný název vlastnosti, která se má zobrazit v uživatelském mj. |
| WorkflowPropertyDescription        | nvarchar(255) | Ano         | Popis nemovitosti |
| Hodnota PropertyValue              | nvarchar(255) | Ne          | Hodnota nemovitosti uložené ve smlouvě |
| Název státu                          | nvarchar(50)  | Ano         | Pokud tato vlastnost obsahuje stav, je zobrazovaný název pro stav. Pokud není přidružen ke stavu, bude hodnota null. |
| Název_zobrazení stavu                   | nvarchar(255) | Ne          | Pokud tato vlastnost obsahuje stav, je zobrazovaný název pro stav. Pokud není přidružen ke stavu, bude hodnota null. |
| Hodnota stavu                         | nvarchar(255) | Ano         | Pokud tato vlastnost obsahuje stav, je hodnota stavu. Pokud není přidružen ke stavu, bude hodnota null. |

## <a name="vwuser"></a>uživatel vw

Toto zobrazení obsahuje podrobnosti o členy konsorcia, kteří jsou zřízeny k použití Azure Blockchain Workbench. Ve výchozím nastavení jsou data naplněna počátečním zřizováním uživatele.

| Name (Název)               | Typ          | Může být null | Popis                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | Ne          | Jedinečný identifikátor uživatele |
| ExternalID         | nvarchar(255) | Ne          | Externí identifikátor pro uživatele. Ve výchozím nastavení je toto ID identifikátorem identifikátor u uživatelů. |
| ZřizováníStav | int           | Ne          |Identifikuje aktuální stav procesu zřizování pro uživatele. Možné hodnoty: <br />0 – Uživatel byl vytvořen rozhraním API<br />1 – Klíč byl přidružen k uživateli v databázi<br />2 – Uživatel je plně zřízený |
| FirstName          | nvarchar(50)  | Ano         | Křestní jméno uživatele |
| LastName           | nvarchar(50)  | Ano         | Příjmení uživatele |
| Emailaddress       | nvarchar(255) | Ano         | E-mailová adresa uživatele |

## <a name="vwworkflow"></a>vwWorkflow

Toto zobrazení představuje podrobnosti základní metadata pracovního postupu, stejně jako funkce a parametry pracovního postupu. Je určen pro vytváření sestav, obsahuje také metadata o aplikaci přidružené k pracovnímu postupu. Toto zobrazení obsahuje data z více podkladových tabulek pro usnadnění vytváření sestav o pracovních postupech. Pro každý pracovní postup obsahuje toto zobrazení následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Informace o stavu spuštění přidruženého pracovního postupu

| Name (Název)                              | Typ          | Může být null | Popis                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Ne          | Jedinečný identifikátor aplikace |
| ApplicationName                   | nvarchar(50)  | Ne          | Název aplikace |
| Název_applicationdisplayname            | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                | bitové           | Ne          | Identifikuje, zda je aplikace povolena. |
| Id pracovního postupu                        | int           | Ano         | Jedinečný identifikátor pracovního postupu |
| Název pracovního postupu                      | nvarchar(50)  | Ne          | Název pracovního postupu |
| Název_pracovního postupuDisplayName               | nvarchar(255) | Ne          | Název zobrazený v uživatelském rozhraní |
| Popis pracovního postupu               | nvarchar(255) | Ano         | Popis pracovního postupu. |
| WorkflowConstructorFunctionId     | int           | Ne          | Identifikátor funkce pracovního postupu, která slouží jako konstruktor pracovního postupu |
| WorkflowStartStateId              | int           | Ne          | Jedinečný identifikátor pro stát |
| Název_pracovního postupu_Spuštění_státu            | nvarchar(50)  | Ne          | Název státu |
| WorkflowStartStateDisplayName     | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní pro stav |
| WorkflowStartStateDescription     | nvarchar(255) | Ano         | Popis stavu pracovního postupu |
| WorkflowStartStateStyle           | nvarchar(50)  | Ano         | Tato hodnota určuje procento dokončení, které je pracovní postup v tomto stavu |
| WorkflowStartStateValue           | int           | Ne          | Hodnota státu |
| Pracovní postup StartStatePercentComplete | int           | Ne          | Textový popis, který poskytuje nápovědu klientům o tom, jak vykreslit tento stav v unovém počítači. Mezi podporované stavy patří *úspěch* a *neúspěch* |

## <a name="vwworkflowfunction"></a>funkce vwWorkflow

Toto zobrazení představuje podrobnosti základní metadata pracovního postupu, stejně jako funkce a parametry pracovního postupu. Je určen pro vytváření sestav, obsahuje také metadata o aplikaci přidružené k pracovnímu postupu. Toto zobrazení obsahuje data z více podkladových tabulek pro usnadnění vytváření sestav o pracovních postupech. Pro každou funkci pracovního postupu obsahuje toto zobrazení následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Podrobnosti funkce pracovního postupu

| Name (Název)                                 | Typ          | Může být null | Popis                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Ne          | Jedinečný identifikátor aplikace |
| ApplicationName                      | nvarchar(50)  | Ne          | Název aplikace |
| Název_applicationdisplayname               | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled                   | bitové           | Ne          | Identifikuje, zda je aplikace povolena. |
| Id pracovního postupu                           | int           | Ne          | Jedinečný identifikátor pracovního postupu |
| Název pracovního postupu                         | nvarchar(50)  | Ne          | Název pracovního postupu |
| Název_pracovního postupuDisplayName                  | nvarchar(255) | Ne          | Název pracovního postupu zobrazený v uživatelském rozhraní |
| Popis pracovního postupu                  | nvarchar(255) | Ano         | Popis pracovního postupu |
| Pracovní postupFunctionId                   | int           | Ne          | Jedinečný identifikátor funkce |
| Název funkce pracovního postupu                 | nvarchar(50)  | Ano         | Název funkce |
| Název_displeje WorkflowFunction          | nvarchar(255) | Ne          | Název funkce, která má být zobrazena v uživatelském rozhraní |
| WorkflowFunctionDescription          | nvarchar(255) | Ano         | Popis funkce pracovního postupu |
| WorkflowFunctionIsConstructor        | bitové           | Ne          | Identifikuje, zda je funkce pracovního postupu konstruktorem pracovního postupu. |
| WorkflowFunctionID          | int           | Ne          | Jedinečný identifikátor parametru funkce |
| Název_parametru WorkflowFunctionFunctionName        | nvarchar(50)  | Ne          | Název parametru funkce |
| Název_displeje WorkflowFunctionFunctionParameterName | nvarchar(255) | Ne          | Název parametru funkce, který má být zobrazen v uživatelském rozhraní |
| WorkflowFunctionFunctionParametrId  | int           | Ne          | Jedinečný identifikátor datového typu přidruženého k parametru funkce pracovního postupu |
| WorkflowParameterNázev_typu__        | nvarchar(50)  | Ne          | Název datového typu přidruženého k parametru funkce pracovního postupu |

## <a name="vwworkflowproperty"></a>vwWorkflowNemovitost

Toto zobrazení představuje vlastnosti definované pro pracovní postup. Pro každou vlastnost obsahuje toto zobrazení následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Podrobnosti vlastností pracovního postupu

| Name (Název)                         | Typ          | Může být null | Popis                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Ne          | Jedinečný identifikátor aplikace |
| ApplicationName              | nvarchar(50)  | Ne          | Název aplikace |
| Název_applicationdisplayname       | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| ApplicationEnabled           | bitové           | Ne          | Identifikuje, zda je aplikace aktuálně povolena.<br />**Poznámka:** I když aplikace může být v databázi zakázána, přidružené smlouvy zůstávají na blockchainu a data o těchto smlouvách zůstávají v databázi. |
| Id pracovního postupu                   | int           | Ne          | Jedinečný identifikátor pracovního postupu |
| Název pracovního postupu                 | nvarchar(50)  | Ne          | Název pracovního postupu |
| Název_pracovního postupuDisplayName          | nvarchar(255) | Ne          | Název, který se má zobrazit pro pracovní postup v uživatelském rozhraní |
| Popis pracovního postupu          | nvarchar(255) | Ano         | Popis pracovního postupu |
| Id vlastností pracovního postupu           | int           | Ne          | Jedinečný identifikátor vlastnosti pracovního postupu |
| Název_pracovního postupu         | nvarchar(50)  | Ne          | Název vlastnosti |
| WorkflowPropertyDescription  | nvarchar(255) | Ano         | Popis nemovitosti |
| Název_displeje_pracovního postupu  | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní |
| WorkflowWorkflowId pracovního postupu pracovního postupu pracovního postupu   | int           | Ne          | ID pracovního postupu, ke kterému je tato vlastnost přidružena |
| WorkflowPropertyDataTypeId   | int           | Ne          | ID datového typu definovaného pro vlastnost |
| WorkflowPropertyNázev_name | nvarchar(50)  | Ne          | Název datového typu definovaného pro vlastnost |
| WorkflowPropertyIsState      | bitové           | Ne          | Toto pole identifikuje, zda tato vlastnost pracovního postupu obsahuje stav pracovního postupu. |

## <a name="vwworkflowstate"></a>vwWorkflowState

Toto zobrazení představuje vlastnosti přidružené k pracovnímu postupu. Pro každou smlouvu obsahuje toto zobrazení následující data:

-   Definice přidružené aplikace
-   Přidružená definice pracovního postupu
-   Informace o stavu pracovního postupu

| Name (Název)                         | Typ          | Může být null | Popis                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Ne          | Jedinečný identifikátor aplikace |
| ApplicationName              | nvarchar(50)  | Ne          | Název aplikace |
| Název_applicationdisplayname       | nvarchar(255) | Ne          | Popis žádosti |
| ApplicationEnabled           | bitové           | Ne          | Identifikuje, zda je aplikace aktuálně povolena.<br />**Poznámka:** I když aplikace může být v databázi zakázána, přidružené smlouvy zůstávají na blockchainu a data o těchto smlouvách zůstávají v databázi. |
| Id pracovního postupu                   | int           | Ne          | Jedinečný identifikátor pracovního postupu |
| Název pracovního postupu                 | nvarchar(50)  | Ne          | Název pracovního postupu |
| Název_pracovního postupuDisplayName          | nvarchar(255) | Ne          | Název zobrazený v uživatelském rozhraní pracovního postupu |
| Popis pracovního postupu          | nvarchar(255) | Ano         | Popis pracovního postupu |
| WorkflowStateID              | int           | Ne          | Jedinečný identifikátor stavu |
| Název_pracovního postupu            | nvarchar(50)  | Ne          | Název státu |
| WorkflowStateDisplayName     | nvarchar(255) | Ne          | Název, který se má zobrazit v uživatelském rozhraní pro stav |
| WorkflowStateDescription     | nvarchar(255) | Ano         | Popis stavu pracovního postupu |
| Pracovní ho skonto | int           | Ne          | Tato hodnota určuje procento dokončení, které je pracovní postup v tomto stavu |
| Hodnota pracovního postupu           | nvarchar(50)  | Ne          | Hodnota státu |
| WorkflowStateStyle           | nvarchar(50)  | Ne          | Textový popis, který poskytuje nápovědu klientům o tom, jak vykreslit tento stav v unovém počítači. Mezi podporované stavy patří *úspěch* a *neúspěch* |
