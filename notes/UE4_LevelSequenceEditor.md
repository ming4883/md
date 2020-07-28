FLevelSequenceEditorToolkit::Initialize()

```cpp
Sequencer = FModuleManager::LoadModuleChecked<ISequencerModule>("Sequencer").CreateSequencer(SequencerInitParams);
SpawnRegister->SetSequencer(Sequencer);
Sequencer->OnActorAddedToSequencer().AddSP(this, &FLevelSequenceEditorToolkit::HandleActorAddedToSequencer);

FLevelEditorSequencerIntegration::Get().AddSequencer(Sequencer.ToSharedRef(), Options);
ULevelSequenceEditorBlueprintLibrary::SetSequencer(Sequencer.ToSharedRef());
```

Detect Sequencer is opened and editing

```cpp

for(auto UObj : GEditor->GetEditorSubsystem<UAssetEditorSubsystem>()->GetAllEditedAssets())
{
  auto Seq = Cast<ULevelSequence>(UObj);
  if (IsValid(Seq))
  {
    for(auto Binding : Seq->GetMovieScene()->GetBindings())
    {
      if (Binding.GetName().Contains(TargetActorName, ESearchCase::IgnoreCase))
      {
        return true;
      }
    }
  }
}

```
