getcontactdata.js

```Plain
var dueDate = IndexData.GetField("Due_Date");
var invoiceAccountCodeArray = IndexData.GetField("Account_Code");


if (IndexData.GetField("Use_Custom_Payment_Terms") === 1)
{
    UpdatePaymentTerms();
}

if (IndexData.GetField("Use_Custom_Purchase_Settings") === 1)
{
    UpdatePurchaseDetails();
}


function UpdatePurchaseDetails()
{
    var accountCode = undefined;
    var tracking1 = undefined;
    var tracking2 = undefined;
    if (RESTCall.StatusCode === 200)
    {
        // get the account code
        var accountCodeArray = Json.GetValues("Contacts[].PurchasesDefaultAccountCode");
        if (Array.isArray(accountCodeArray) && accountCodeArray.length > 0) { accountCode = accountCodeArray[0]; }
    }
    if (!accountCode && IndexData.GetField("Default_Account"))
    {
        accountCode = IndexData.GetField("Default_Account");
    }
    if (accountCode)
    {
        for (var i = 0; i < invoiceAccountCodeArray.length; i++)
        {
            if (!invoiceAccountCodeArray[i]) { invoiceAccountCodeArray[i] = accountCode; }
        }
    }
}


function UpdatePaymentTerms()
{
    var typeValue = undefined;
    var dayValue = undefined;
    var invDate = IndexData.GetField("Invoice_Date");

    if (RESTCall.StatusCode === 200)
    {
        var dayArray = Json.GetValues("Contacts[].PaymentTerms.Bills.Day");
        if (dayArray) { dayValue = parseInt(dayArray[0]); }
        var typeArray = Json.GetValues("Contacts[].PaymentTerms.Bills.Type");
        if (typeArray) { typeValue = typeArray[0]; }
    }

    if (!typeValue || !dayValue)
    {
        typeValue = IndexData.GetField("Default_Payment_Type_Text");
        dayValue = IndexData.GetField("Default_Day_Due");
    }

    var tempDate = GetDueDate(
                                invDate,
                                dayValue,
                                typeValue
                            );
    if (tempDate) { dueDate = tempDate; }
}

function GetDueDate(invoiceDate, day, paymentTerms)
{
    if (invoiceDate && day && paymentTerms)
    {
        var nowDate = new Date();
        switch (paymentTerms)
        {
            case 'DAYSAFTERBILLDATE':
                invoiceDate.setDate(invoiceDate.getDate() + day);
                return invoiceDate;
                break;
            case 'DAYSAFTERBILLMONTH':
                invoiceDate.setMonth(invoiceDate.getMonth() + 1);
                invoiceDate.setDate(day);
                return invoiceDate;
                break;
            case 'OFCURRENTMONTH':
                invoiceDate.setMonth(nowDate.getMonth());
                invoiceDate.setDate(day);
                return invoiceDate;
                break;
            case 'OFFOLLOWINGMONTH':
                invoiceDate.setMonth(nowDate.getMonth() + 1);
                invoiceDate.setDate(day);
                return invoiceDate;
                break;
            default:
                return undefined;
        }
    }
}
```