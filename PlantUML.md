@startuml
!theme vibrant
!pragma teoz true
title DFC - Color Calibrartion with Default Configuration
actor User as "ODM Operator"

box "APP A"  #LightCyan
    participant "MAIN" as DFC

    box "User Interface Modules" #LightGreen
        participant "MainWindow" as MainUI
        'participant "PlaceJigWindow" as JigUI
    end box
   
    box "Controller" #LightYellow
        participant "ProcessController" as PC
        participant "PatchsetMeasurement" as PAT
    end box


    box "ProfileBuilder" #fae6b9
        participant "ProfileBuilder" as PB
        'participant "ColorCompensationProfileBuilder" as CCB
        'participant "NativeProfileBuilder" as NPB
        'participant "NoMhc2TagProfileBuilder" as MPB


    end box

    box "Model" #E5CEEB
        participant "Prism(Wrapper)" as PSM
        participant "i1D3Device(Wrapper)" as ID
    end box

   
end box
box "SDK" #FFA500
        participant "i1D3SDK.dll" as iDLL
        participant "Prism.dll" as pDLL
        'participant "WinSDK" as Win
end box
/'
box "Common" #ebc2b9
    box "HardwareInfo\n\n" #cbebb9
        participant "DisplayHelper" as DH
    end box     
end box
'/



        note over PC: PC identifies optimal brightness\n close to Target Luminosity


activate PC
PC->PSM:startProfiling
PSM->PSM:Remove any profile\xn if applied early
PSM->PB:Create Instance
activate PB
PB->PB:Load Profile configurations\n to Profile Builder
PB->PB:Sets White Point

	par PrismRunnable
            deactivate PB
            PB-->PC

            PC-> PSM++ #blue: emitNextColorToMeasure()
            deactivate PC
	    loop  For each color patch
                PSM->pDLL:XRColorGetColorSpace(XRColorRef)
                PSM<--pDLL:return:XRColorSpace
                activate MainUI
                PC-->MainUI:signal:showMeasureWindow
                MainUI-->User:Show Color Patch
                deactivate MainUI
                PC->ID:startTriggerMeasurement()\n Tells i1D3device to \ncapture the color \nshown inthe Main window

                activate PC
                ID-> ID++ #red: triggerMeasurementImpl()
                ID->iDLL:i1d3MeasureXYZ()
                ID<--iDLL:return:i1d3XYZ_t color
                ID-->PC:signal:readyForProcessing
                deactivate PC
                ID--> PSM-- #red: done
            end
            PSM--> PC-- #blue: done
        else
        end
activate PC
PC->PC:onStartProcessing()
PC->PSM:startProcessing()
	par PrismRunnable
            deactivate PC
            deactivate PB
            PSM-> PSM++ #blue: startProcessingImpl()

	    loop  For each profiles
                par BuilderThread
                PSM-> PSM++ #red: start()
                else
                end
                PSM--> PSM-- #red: done
                par BuilderThread
                PSM-> PSM++ #green: start()
                else
                end
                PSM--> PSM-- #green: done
                par BuilderThread
                PSM-> PSM++ #yellow: start()
                else
                end
                PSM--> PSM-- #yellow: done
            note over PSM: ICM files are created
            end

            PSM--> PSM-- #blue: done
        else
        end

note over PSM: Measurement report\nonly for color scientist usage
alt if configure
PSM->PSM:writeMeasurementReport()
else
end
activate PC
PSM-->PC:processingFinished
PC->PC:onFinishedProcessing()

alt if configures
PC->PSM:startQualityReport()
        par PrismRunnable
            deactivate PC
            deactivate PB
            activate  PSM
            PSM-> PSM: startProfileVerification()
            PSM->PSM:installNativeProfile()
            PSM->PSM:applyCurrentProfile()



            note over PSM: Software will \nverify the Targets color
            note over PSM: Write Quality report as txt files
            activate PC
            activate MainUI
            PSM-->PC:signal:qualityReportFinishedSuccessfully
            PC-->MainUI:signal:resultSuccess
            deactivate PC
            deactivate MainUI
            deactivate  PSM  @startuml
!theme vibrant
!pragma teoz true
title DFC - Color Calibrartion with Default Configuration
actor User as "ODM Operator"

box "APP A"  #LightCyan
    participant "MAIN" as DFC

    box "User Interface Modules" #LightGreen
        participant "MainWindow" as MainUI
        'participant "PlaceJigWindow" as JigUI
    end box
   
    box "Controller" #LightYellow
        participant "ProcessController" as PC
        participant "PatchsetMeasurement" as PAT
    end box


    box "ProfileBuilder" #fae6b9
        participant "ProfileBuilder" as PB
        'participant "ColorCompensationProfileBuilder" as CCB
        'participant "NativeProfileBuilder" as NPB
        'participant "NoMhc2TagProfileBuilder" as MPB


    end box

    box "Model" #E5CEEB
        participant "Prism(Wrapper)" as PSM
        participant "i1D3Device(Wrapper)" as ID
    end box

   
end box
box "SDK" #FFA500
        participant "i1D3SDK.dll" as iDLL
        participant "Prism.dll" as pDLL
        'participant "WinSDK" as Win
end box
/'
box "Common" #ebc2b9
    box "HardwareInfo\n\n" #cbebb9
        participant "DisplayHelper" as DH
    end box     
end box
'/



        note over PC: PC identifies optimal brightness\n close to Target Luminosity


activate PC
PC->PSM:startProfiling
PSM->PSM:Remove any profile\xn if applied early
PSM->PB:Create Instance
activate PB
PB->PB:Load Profile configurations\n to Profile Builder
PB->PB:Sets White Point

	par PrismRunnable
            deactivate PB
            PB-->PC

            PC-> PSM++ #blue: emitNextColorToMeasure()
            deactivate PC
	    loop  For each color patch
                PSM->pDLL:XRColorGetColorSpace(XRColorRef)
                PSM<--pDLL:return:XRColorSpace
                activate MainUI
                PC-->MainUI:signal:showMeasureWindow
                MainUI-->User:Show Color Patch
                deactivate MainUI
                PC->ID:startTriggerMeasurement()\n Tells i1D3device to \ncapture the color \nshown inthe Main window

                activate PC
                ID-> ID++ #red: triggerMeasurementImpl()
                ID->iDLL:i1d3MeasureXYZ()
                ID<--iDLL:return:i1d3XYZ_t color
                ID-->PC:signal:readyForProcessing
                deactivate PC
                ID--> PSM-- #red: done
            end
            PSM--> PC-- #blue: done
        else
        end
activate PC
PC->PC:onStartProcessing()
PC->PSM:startProcessing()
	par PrismRunnable
            deactivate PC
            deactivate PB
            PSM-> PSM++ #blue: startProcessingImpl()

	    loop  For each profiles
                par BuilderThread
                PSM-> PSM++ #red: start()
                else
                end
                PSM--> PSM-- #red: done
                par BuilderThread
                PSM-> PSM++ #green: start()
                else
                end
                PSM--> PSM-- #green: done
                par BuilderThread
                PSM-> PSM++ #yellow: start()
                else
                end
                PSM--> PSM-- #yellow: done
            note over PSM: ICM files are created
            end

            PSM--> PSM-- #blue: done
        else
        end

note over PSM: Measurement report\nonly for color scientist usage
alt if configure
PSM->PSM:writeMeasurementReport()
else
end
activate PC
PSM-->PC:processingFinished
PC->PC:onFinishedProcessing()
alt if configures
PC->PSM:startQualityReport()
        par PrismRunnable
            deactivate PC
            deactivate PB
            activate  PSM
            PSM-> PSM: startProfileVerification()
            PSM->PSM:installNativeProfile()
            PSM->PSM:applyCurrentProfile()

            deactivate  PSM  

            note over PSM: Software will \nverify the Targets color
            note over PSM: Write Quality report as txt files
            PSM-->PC:signal:qualityReportFinishedSuccessfully
            PC-->MainUI:signal:resultSuccess
            activate MainUI
            activate DFC

            MainUI->MainUI:Auto-close
            MainUI-->DFC:exits(SUCCESS)
            deactivate MainUI
            deactivate DFC

            
        else
            PC-->MainUI:signal:resultFailure
            activate DFC
            activate MainUI
            MainUI->MainUI:Auto-close
            MainUI-->DFC:exits(Failure)
            deactivate PC
            deactivate DFC
            deactivate MainUI
        end

else ELSE - skips Quality Report Generation
activate PC
activate MainUI
PC-->MainUI:signal:resultSuccess
MainUI->MainUI:Auto-close
deactivate PC
activate DFC
MainUI-->DFC:exits(SUCCESS)
end
deactivate PB
deactivate PSM
deactivate MainUI
deactivate DFC
@enduml
