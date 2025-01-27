---
title: "ASP.NET Compatibility"
ms.date: "03/30/2017"
ms.assetid: c8b51f1e-c096-4c42-ad99-0519887bbbc5
---
# ASP.NET Compatibility
This sample demonstrates how to enable ASP.NET Compatibility mode in Windows Communication Foundation (WCF). Services running in ASP.NET Compatibility mode participate fully in the ASP.NET application pipeline and can make use of ASP.NET features such as file/URL authorization, session state, and the <xref:System.Web.HttpContext> class. The <xref:System.Web.HttpContext> class allows access to cookies, sessions, and other ASP.NET features. This mode requires that the bindings use the HTTP transport and the service itself must be hosted in IIS.  
  
 In this sample, the client is a console application (an executable) and the service is hosted in Internet Information Services (IIS).  
  
> [!NOTE]
> The set-up procedure and build instructions for this sample are located at the end of this topic.  
  
This sample requires a [!INCLUDE[netfx40_long](../../../../includes/netfx40-long-md.md)] application pool in order to run. To create a new application pool, or to modify the default application pool, follow these steps.  

1. Open **Control Panel**.  Open the **Administrative Tools** applet under the **System and Security** heading. Open the **Internet Information Services (IIS) Manager** applet.  

2. Expand the treeview in the **Connections** pane. Select the **Application Pools** node.  

3. To set the default application pool to use [!INCLUDE[netfx40_long](../../../../includes/netfx40-long-md.md)] (which may cause incompatibility problems with existing sites), right-click the **DefaultAppPool** list item and select **Basic Settings…**. Set the **.Net Framework Version** pull-down to **.Net Framework v4.0.30128** (or later).  

4. To create a new application pool that uses [!INCLUDE[netfx40_long](../../../../includes/netfx40-long-md.md)] (to preserve compatibility for other applications), right-click the **Application Pools** node and select **Add Application Pool…**. Name the new application pool, and set the **.Net Framework Version** pull-down to **.Net Framework v4.0.30128** (or later). After running the setup steps below, right-click the **ServiceModelSamples** application and select **Manage Application**, **Advanced Settings…**. Set the **Application Pool** to the new application pool.  
  
> [!IMPORTANT]
>  The samples may already be installed on your computer. Check for the following (default) directory before continuing.  
>   
>  `<InstallDrive>:\WF_WCF_Samples`  
>   
>  If this directory does not exist, go to [Windows Communication Foundation (WCF) and Windows Workflow Foundation (WF) Samples for .NET Framework 4](https://go.microsoft.com/fwlink/?LinkId=150780) to download all Windows Communication Foundation (WCF) and [!INCLUDE[wf1](../../../../includes/wf1-md.md)] samples. This sample is located in the following directory.  
>   
>  `<InstallDrive>:\WF_WCF_Samples\WCF\Basic\Services\Hosting\WebHost\ASPNetCompatibility`  
  
 This sample is based on the [Getting Started](../../../../docs/framework/wcf/samples/getting-started-sample.md), which implements a calculator service. The `ICalculator` contract has been modified as the `ICalculatorSession` contract to allow a set of operations to be performed, while keeping a running result.  
  
```csharp  
[ServiceContract(Namespace="http://Microsoft.ServiceModel.Samples")]  
public interface ICalculatorSession  
{  
    [OperationContract]  
    void Clear();  
    [OperationContract]  
    void AddTo(double n);  
    [OperationContract]  
    void SubtractFrom(double n);  
    [OperationContract]  
    void MultiplyBy(double n);  
    [OperationContract]  
    void DivideBy(double n);  
    [OperationContract]  
    double Result();  
}  
```  
  
 The service maintains state, using the feature, for each client as multiple service operations are called to perform a calculation. The client can retrieve the current result by calling `Result` and can clear the result to zero by calling `Clear`.  
  
 The service uses the ASP.NET session to store the result for each client session. This allows the service to maintain the running result for each client across multiple calls to the service.  
  
> [!NOTE]
> ASP.NET session state and WCF sessions are very different things. See [Session](../../../../docs/framework/wcf/samples/session.md) for details on WCF sessions.
  
 The service has an intimate dependency on ASP.NET session state and requires ASP.NET compatibility mode to function correctly. These requirements are expressed declaratively by applying the `AspNetCompatibilityRequirements` attribute.  
  
```csharp  
[AspNetCompatibilityRequirements(RequirementsMode =  
                       AspNetCompatibilityRequirementsMode.Required)]  
public class CalculatorService : ICalculatorSession  
{  
    double Result  
    {  // store result in AspNet Session  
       get {  
          if (HttpContext.Current.Session["Result"] != null)  
             return (double)HttpContext.Current.Session["Result"];  
          return 0.0D;  
       }  
       set  
       {  
          HttpContext.Current.Session["Result"] = value;  
       }  
    }  
    public void Clear()  
    {  
        Result = 0.0D;  
    }  
    public void AddTo(double n)  
    {  
        Result += n;  
    }  
    public void SubtractFrom(double n)  
    {  
        Result -= n;  
    }  
    public void MultiplyBy(double n)  
    {  
        Result *= n;  
    }  
    public void DivideBy(double n)  
    {  
        Result /= n;  
    }  
    public double Result()  
    {  
        return Result;  
    }  
}  
```
  
 When you run the sample, the operation requests and responses are displayed in the client console window. Press ENTER in the client window to shut down the client.  
  
```console
0, + 100, - 50, * 17.65, / 2 = 441.25  
Press <ENTER> to terminate client.  
```  
  
### To set up, build, and run the sample  
  
1. Be sure you have performed the [One-Time Setup Procedure for the Windows Communication Foundation Samples](../../../../docs/framework/wcf/samples/one-time-setup-procedure-for-the-wcf-samples.md).  
  
2. To build the C# or Visual Basic .NET edition of the solution, follow the instructions in [Building the Windows Communication Foundation Samples](../../../../docs/framework/wcf/samples/building-the-samples.md).  
  
3. After the solution has been built, run Setup.bat to set up the ServiceModelSamples Application in IIS 7.0. The ServiceModelSamples directory should now appear as an IIS 7.0 Application.  
  
4. To run the sample in a single- or cross-computer configuration, follow the instructions in [Running the Windows Communication Foundation Samples](../../../../docs/framework/wcf/samples/running-the-samples.md).  
  
## See also

- [AppFabric Hosting and Persistence Samples](https://go.microsoft.com/fwlink/?LinkId=193961)
