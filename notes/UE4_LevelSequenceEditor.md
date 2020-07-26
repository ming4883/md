FLevelSequenceEditorToolkit::Initialize()

```cpp
Sequencer = FModuleManager::LoadModuleChecked<ISequencerModule>("Sequencer").CreateSequencer(SequencerInitParams);
SpawnRegister->SetSequencer(Sequencer);
Sequencer->OnActorAddedToSequencer().AddSP(this, &FLevelSequenceEditorToolkit::HandleActorAddedToSequencer);

FLevelEditorSequencerIntegration::Get().AddSequencer(Sequencer.ToSharedRef(), Options);
ULevelSequenceEditorBlueprintLibrary::SetSequencer(Sequencer.ToSharedRef());
```
