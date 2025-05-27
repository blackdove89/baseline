
DECLARE @StepId int 
DECLARE @ReportName VARCHAR(100)
DECLARE @StepName VARCHAR(100)

-- Remove "FACES Error File from BRDCRP.DC35Z6" step in "RBC Maintenance" job

SET @StepId = 0
SET @ReportName = 'RBC Maintenance' 
SET @StepName = 'FACES Error File from BRDCRP.DC35Z6' 

SELECT   
   @StepId = step_id
FROM 
   msdb..sysjobs a 
      join msdb..sysjobsteps b on a.job_id = b.job_id
where
   a.name  = @ReportName and 
   b.step_name = @StepName
   
IF @StepId > 0 
BEGIN
   EXEC msdb.dbo.sp_delete_jobstep  @job_name = @ReportName,  @step_id = @StepId
END
;


-- Remove "FACES (FACEBRIEF) Errors from ARDCRP.DC85Z1" step in "RBC Maintenance" job

SET @StepId = 0
SET @ReportName = 'RBC Maintenance' 
SET @StepName = 'FACES (FACEBRIEF) Errors from ARDCRP.DC85Z1' 

SELECT   
   @StepId = step_id
FROM 
   msdb..sysjobs a 
      join msdb..sysjobsteps b on a.job_id = b.job_id
where
   a.name  = @ReportName and 
   b.step_name = @StepName
   
IF @StepId > 0 
BEGIN
   EXEC msdb.dbo.sp_delete_jobstep  @job_name = @ReportName,  @step_id = @StepId
END
;

-- Remove "FACES Error file from ARDCRP.DC64Z1" step in "RBC Maintenance" job

SET @StepId = 0
SET @ReportName = 'RBC Maintenance' 
SET @StepName = 'FACES Error file from ARDCRP.DC64Z1' 

SELECT   
   @StepId = step_id
FROM 
   msdb..sysjobs a 
      join msdb..sysjobsteps b on a.job_id = b.job_id
where
   a.name  = @ReportName and 
   b.step_name = @StepName
   
IF @StepId > 0 
BEGIN
   EXEC msdb.dbo.sp_delete_jobstep  @job_name = @ReportName,  @step_id = @StepId
END
;

-- Remove "Error 41" step in "RBC Maintenance" job

SET @StepId = 0
SET @ReportName = 'RBC Maintenance' 
SET @StepName = 'Error 41' 

SELECT   
   @StepId = step_id
FROM 
   msdb..sysjobs a 
      join msdb..sysjobsteps b on a.job_id = b.job_id
where
   a.name  = @ReportName and 
   b.step_name = @StepName 
   
IF @StepId > 0 
BEGIN
   EXEC msdb.dbo.sp_delete_jobstep  @job_name = @ReportName,  @step_id = @StepId
END
;

-- Add new step "JANUS Triggered Claims Report" in "RBC Maintenance" job

SET @StepId = 0
SET @ReportName = 'RBC Maintenance' 
SET @StepName = 'JANUS Triggered Claims Report' 


-- Since the last step added make the last step SUCCESS/FAIL flow.

SELECT   
   @StepId = Max(step_id) + 1
FROM 
   msdb..sysjobs a 
      join msdb..sysjobsteps b on a.job_id = b.job_id
where
   a.name  = @ReportName 

IF @StepId > 0 
BEGIN
   EXEC msdb.dbo.sp_add_jobstep @job_name=@ReportName, 
		 @step_name=@StepName,
         @step_id=@StepId,  
         @on_success_action=1, 
         @on_fail_action=2
END
GO

-- Cleanup Set Log Files job

DECLARE @ReturnCode INT

BEGIN TRANSACTION

SELECT @ReturnCode = 0
EXEC @ReturnCode = msdb.dbo.sp_update_jobstep @job_name=N'Set Log Files', 
		@step_id=1, 
		@command=N'exec spSetLogFiles ''Process Cases'', 5, ''ProcessCases.Log''
	exec spSetLogFiles ''Process Cases'', 6, ''ProcessPayments.Log''
	exec spSetLogFiles ''Process Cases'', 7, ''ProcessReissueCases.Log''
	exec spSetLogFiles ''Process Mail'', 1, ''ProcessMail.Log'', 1
	exec spSetLogFiles ''Process Mail'', 2, ''ProcessMail.Log'', 1
	exec spSetLogFiles ''Process Mail'', 3, ''ProcessMail.Log'', 1
	exec spSetLogFiles ''Process Mail'', 4, ''ProcessMail.Log'', 1
	exec spSetLogFiles ''Process Mail'', 5, ''ProcessMail.Log'', 1
	exec spSetLogFiles ''RBC Maintenance'', 1, ''RBC Maintenance.Log''
	exec spSetLogFiles ''RBC Maintenance'', 2, ''RBC Maintenance.Log''
	exec spSetLogFiles ''RBC Maintenance'', 3, ''RBC Maintenance.Log''
	exec spSetLogFiles ''RBC Maintenance'', 4, ''RBC Maintenance.Log''
	exec spSetLogFiles ''RBC Maintenance'', 5, ''RBC Maintenance.Log''
	exec spSetLogFiles ''RBC Maintenance'', 6, ''RBC Maintenance.Log''
	exec spSetLogFiles ''RBC Maintenance'', 7, ''RBC Maintenance.Log''
	exec spSetLogFiles ''RBC Maintenance'', 8, ''RBC Maintenance.Log''
	exec spSetLogFiles ''Process MF Data'', 1, ''ProcessMFData.Log'', 2
	exec spSetLogFiles ''Process MF Data'', 2, ''ProcessMFData.Log'', 2
	exec spSetLogFiles ''Process MF Data'', 3, ''ProcessMFData.Log'', 2
	exec spSetLogFiles ''Process MF Data'', 4, ''ProcessMFData.Log'', 2
	exec spSetLogFiles ''Process MF Data'', 5, ''ProcessMFData.Log'', 2
	exec spSetLogFiles ''Process MF Data'', 6, ''ProcessMFData.Log'', 2
	exec spSetLogFiles ''Process MF Data'', 7, ''ProcessMFData.Log'', 2
	exec spSetLogFiles ''Process MF Data'', 8, ''ProcessMFData.Log'', 2
	exec spSetLogFiles ''Process MF Data'', 9, ''ProcessMFData.Log'', 2
	exec spSetLogFiles ''Process MF Data'', 10, ''ProcessMFData.Log'', 2
	exec spSetLogFiles ''Process MF Data'', 11, ''ProcessMFData.Log'', 2
	exec spSetLogFiles ''Process MF Data'', 12, ''ProcessMFData.Log'', 2
	exec spSetLogFiles ''Process MF Data'', 13, ''ProcessMFData.Log'', 2
	exec spSetLogFiles ''Process MF Data'', 14, ''ProcessMFData.Log'', 2
	exec spSetLogFiles ''Process LS Return'', 2, ''ProcessLSReturn.Log''
	'
IF (@@ERROR <> 0 OR @ReturnCode <> 0) 
BEGIN
	ROLLBACK TRANSACTION
END
ELSE 
BEGIN
	COMMIT TRANSACTION
END
GO


-- Delete "SQL Report for QCAB: PA Support System Self Service Transaction Count" SQL job report.
-- since "PA Support System Self Service Transaction Count" report is not required by RS.
IF EXISTS(select   
   1
from 
   msdb..sysjobs a 
where
   a.name  = 'SQL Report for QCAB: PA Support System Self Service Transaction Count' ) 
BEGIN
   EXEC msdb.dbo.sp_delete_job @job_name = 'SQL Report for QCAB: PA Support System Self Service Transaction Count'
END
GO
