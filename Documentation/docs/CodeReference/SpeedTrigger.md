
# Speed Trigger Object

> Speed Trigger objects monitor movers until they have exceeded a specified velocity.

---

## Setup & Execution

```javascript
// Declaration
SpeedTriggerA		: SpeedTrigger;
```

```javascript
// Initization
SpeedTriggerA.ThresholdVelocity		:= 800;
```

---

## Properties

### .MoverInVelocity

> Status boolean indicating that at least one mover is currently exceeding the threshold velocity

---

### .CurrentMover

> Reference variable that refers to the earliest registered mover to exceed the threshold velocity since being registered

- If no movers are tracked, or no tracked movers currently satisfy the objective, then .CurrentMover is an invalid reference. Attempting to evaluate it will result in a page fault and XAR will stop.

- It is recommended that all evaluations are nested inside IF check for .MoverInVelocity OR by calling [__ISVALIDREF](https://infosys.beckhoff.com/english.php?content=../content/1033/tc3_plc_intro/2529165707.html&id=)

- It is possible that multiple registered movers simultaneously exceed the threshold velocity. Only the least recently valid mover handle is provided by .CurrentMover. To access more recent events, deregister the current mover with *SpeedTrigger.UnregisterCurrent()*

---

### .ThresholdVelocity

> Current velocity, above which a mover is considered 'In Velocity'

---

## Extra Examples

```javascript
// Basic Speed Trigger implementation

// Begin monitoring Mover 1
IF bInit THEN
	Mover[1].MoveToPosition( 1000 );
	SpeedTrigger.ThresholdVelocity	:= 600; // mm/s
	bInit	:= FALSE;
END_IF

// When Mover 1 accelerates past 600mm/s, slow it down to 100mm/s
IF SpeedTrigger.MoverInVelocity THEN
	SpeedTrigger.CurrentMover.SetVelocity( 100 );
	SpeedTrigger.UnregisterCurrent();
END_IF
```