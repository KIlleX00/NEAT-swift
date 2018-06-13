# NEAT Implementation With Swift 4

This implementation is based on K.O. Stanley and R. Miikkulainen's paper.
  http://nn.cs.utexas.edu/downloads/papers/stanley.ec02.pdf
  
NEAT stands for Neural Evolution of Augmenting Topologies. Just as our brain creates neurons to keep learned things accessible, this algorithm searches spaces minimally by aslo creating 'neurons' in order to find a solution.





- If you're creating a Swift executable, you can simply add the package to your app.


To use NEAT-swift inside of an XCode project:

- Import the package into your project
![alt text](http://www.troydeville.com/wp-content/uploads/2018/06/neatImport.png)

- Import the module into your chosen file.
```Swift
import Neat
```

- Create a NEAT network.
```Swift
let network = Neat(inputs: 2, outputs: 1, population: 300, confFile: nil, multithread: false)
```
NOTE: "confFile" and "multithread" are not fully functional.
  "confFile" is not functional mostly because of the inability to edit mutation percentages.
  "multithread" is functional, but only for 8 threads (i.e. most quad-core processors).
  - Support for other thread amounts will be coming soon. For now, 1 thread or 8 threads are supported.
  

Typical implementation for a single thread NEAT network (network to be tested until a solution is found or close to it).
```Swift

let input = [[0.0, 0.0], [0.0, 1.0], [1.0, 0.0], [1.0, 1.0]]
let expected = [[0.0], [1.0], [1.0], [0.0]]

let fitnessGoal = 16.0
var generation = 1

var king: NGenome?

for _ in 1...network.populationSize {//testing entire population
        
        var sumedTotal = 0.0
        
        // Test the Genome Pool
        for i in 0..<input.count {
            let output = network.run(inputs: input[i], inputCount: inputs, outputCount: outputs)
            for o in 0..<output.count {
                sumedTotal += abs(expected[i][o] - output[o])
            }
        }
        
        // A fitness function
        let currentGenomeFitness = pow(sqrt(fitnessGoal) - sumedTotal, 2)       // Assign genome a fitness score from the test.
        
        // conditions
        if currentGenomeFitness > HighestFitness { HighestFitness = currentGenomeFitness }
        //if HighestFitness >= fitnessGoal*0.99 { break }
        
        network.nextGenome(currentGenomeFitness)             // Next.
    }
    
    // Do NEAT here.
    network.epoch()
    
    king = network.getKing()
    
    print(network.description)
    print("Generation: \(generation)")
    generation += 1
    
    if king!.fitness >= fitnessGoal*0.99 {
        print(king!.description)
        break
    }
    
    print(king!.description)
```
