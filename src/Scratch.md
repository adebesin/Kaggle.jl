import StatsPlots: plot, plotlyjs, plot_function

####################################################################################################
# TITANIC
###################################################################################################

plotlyjs()

plot(plot_function(x -> x * x, [1, 2, 3, 4, 5]))


survivedcount = count(x -> x == 1,  train.Survived)
diedcount = count(x -> x == 0,  train.Survived)

statsplots.histogram(group = mllabelutils.convertlabel(["Died", "Survived"], train.Survived), train.Fare)


mllabelutils.convertlabel(["Died", "Survived"], train.Survived)

ctg = repeat(["Category 1", "Category 2"], inner = 5)

statsplots.@df train[[:Survived, :Fare]] statsplots.boxplot(:Survived, :Fare)

mllabelutils.convertlabel(["Died", "Survived"], train.Survived)

#=
Why no fstatistic?

GeneralizedLinearModels do support an analysis of deviance, which is similar in
spirit to analysis of variance but, I believe, is equivalent to
a likelihood ratio test
=#

glm.deviance(logreg)

logreg
glm.confint(logreg)
glm.confint(mlogreg)
train[:Prediction] = glm.predict(logreg)
diedsurvived = mllabelutils.convertlabel(["Died", "Survived"], train.Survived)
flux.sigmoid(-3.5041 + 0.4049) # Page 135 of Introduction to Statistical Learning

distributions.Normal(statsbase.mean(x), statsbase.std(x))


gendersubmission = csv.File("./resource/gender_submission.csv") |> csv.DataFrame
train = csv.File("./resource/train.csv") |> csv.DataFrame
test = csv.File("./resource/test.csv") |> csv.DataFrame
menache = csv.File("~/Data/menarche.csv") |> dataframes.DataFrame

menache[:Total] = convert(Array{Int, 1}, menache[:Total])
xGrid = 0:0.1:maximum(menache[:Age])

logreg = glm.glm(
    glm.@formula(Total ~ Age),
    menache,
    glm.Binomial(),
    glm.LogitLink()
)

statsplots.plot(xGrid, glm.predict(logreg))


########################
## Feature Extraction ##
########################

dataframes.head(train)
dataframes.size(train) # shape
dataframes.describe(train)

train[:Fare] = convert(Array{Float64, 1}, train[:Fare])

## Outliers

faremean = statsbase.mean(train.Fare)
farestd = statsbase.std(train.Fare)

# Estimate mean and standard deviation
faredist = distributions.fit(distributions.Normal, train.Fare)
# View distribution
statsplots.plot(faredist)
# View outliers
statsplots.boxplot(train.Fare)
statsbase.iqr(train.Fare)

statsplots.boxplot(statsbase.zscore(train.Fare))
statsbase.zscore(train.Fare)

statsplots.scatter(statsbase.zscore(train.Fare))
statsplots.scatter(train.Fare)

train[:Fare] = statsbase.zscore(train.Fare)

statsplots.plot(statsplots.plot_function(glm.logistic, train.Survived))
#=2
Shows difference in people who paid more survived vs people who paid less
tended to not survive.
=#

#People who paid more were more likely to survive
statsplots.boxplot(train.Survived, train.Fare)
statsplots.bar(train.Survived, train.Fare)

train = train[[:Survived, :Pclass, :Sex, :Age, :SibSp, :Parch, :Fare]]
train = dataframes.dropmissing(train, :Age)

dat1aframes.describe(train)
train.Sex = mllabelutils.convertlabel([0, 1], train.Sex) # zero = male, female = 1

# TODO analysis on what predictors are most relevent to predicting the response variable
logreg = glm.glm(
    glm.@formula(Survived ~ Pclass),
    train,
    glm.Binomial(),
    glm.LogitLink()
)

logreg = glm.glm(
    glm.@formula(Survived ~ Fare),
    train,
    glm.Binomial(),
    glm.LogitLink()
)


xGrid = 0:1:maximum(train[:Fare])
statsplots.plot(xGrid, glm.predict(logreg), seriestype = :line)

glm.predict(logreg)
dataframes.DataFrame(
    Class = train.Pclass,
    Prediction = glm.predict(logreg)
)

hcat([1, 2], [3, 4])

#=
Make predictions: Use the intercept (first coeefficienct)
with the second coefficient (for Pclass)
=#
firstclass, secondclass, thirdclass = 1, 2, 3
flux.sigmoid(glm.coef(logreg)[1] + glm.coef(logreg)[2] * firstclass)
flux.sigmoid(glm.coef(logreg)[1] + glm.coef(logreg)[2] * secondclass)
flux.sigmoid(glm.coef(logreg)[1] + glm.coef(logreg)[2] * thirdclass)
GLM.predict(logreg, dataframes.DataFrame(Survived = [1], Pclass = [3]))
glm.predict(logreg)

pred(x) = 1/(1+exp(-(glm.coef(logreg)[1] + glm.coef(logreg)[2]*x)))

xGrid = 0:0.1:maximum(train[:Pclass])

statsplots.plot(xGrid, pred.(xGrid))

mlogreg = glm.glm(
    glm.@formula(Survived ~ Pclass + Sex + Age + Parch + Fare),
    train,
    glm.Binomial(),
    glm.LogitLink()
)


statsplots.plot(statsplots.plot_function(glm.logistic, train.Survived))


menarche = CSV.File("/Users/greade01/Data/menarche.csv") |> DataFrames.DataFrame
menarche[:Total] = convert(Array{Int, 1}, menarche[:Total])
menarche[:MenarcheTotal] = menarche[:Menarche] ./ menarche[:Total]

StatsPlots.plot(menarche[:Age], menarche[:MenarcheTotal])


DataFramesMeta.@where(train, :Name, occursin.("Mr"))
DataFramesMeta.@select(train, filter(name -> occursin(String(name), "Mr")))
DataFramesMeta.@select(train, filter(name -> occursin(String(name), "Month"), names(train)))

DataFramesMeta.@select(train, [occursin("Name", String(name)) for name in names(train)])

train[occursin.("Mr.", train[:Name]), :]

mrmask = occursin.("Mr.", train[:Name])
train[:Name][mrmask]

DataFrames.by(train, :Title, DataFrames.nrow)


function title(names::Array{String, 1})
    acc::Array{String} = []
    for name in names
        occursin("Mr.", name) && push!(acc, "Mr")
        occursin("Mrs.", name) && push!(acc, "Mrs")
        occursin("Miss.", name) && push!(acc, "Miss")
        occursin("Master.", name) && push!(acc, "Master")
        occursin("Rev.", name) && push!(acc, "Rev")
        push!(acc, "Unknown")
    end
    acc
end

MLLabelUtils.labelfreq(train[:Title])

MLLabelUtils.label(
    MLLabelUtils.labelenc(train[:Title])
)

Flux.onehot("Mr", train[:Title])

# Pertial
g = Base.Fix1((x, y) -> x + y, 1)


occursin("m", skipmissing(["m", missing]))

skipmissing(missing) |> collect
<<<<<<< HEAD

round.([0.231]; digits = 2)

StatsBase.crosscor(train.S, train.C)


#=2
Shows difference in people who paid more survived vs people who paid less
tended to not survive.
=#

statsplots.boxplot(train.Survived, train.Fare)
statsplots.bar(train.Survived, train.Fare)

train = train[[:Survived, :Pclass, :Sex, :Age, :SibSp, :Parch, :Fare]]
train = dataframes.dropmissing(train, :Age)

dataframes.describe(train)
train.Sex = mllabelutils.convertlabel([0, 1], train.Sex) # zero = male, female = 1

logreg = glm.glm(
    glm.@formula(Survived ~ Pclass),
    train,
    glm.Binomial(),
    glm.LogitLink()
)
#=
Make predictions: Use the intercept (first coeefficienct)
with the second coefficient (for Pclass)
=#
firstclass, secondclass, thirdclass = 1, 2, 3
flux.sigmoid(coef(logreg)[1] + coef(logreg)[2] * firstclass)
flux.sigmoid(coef(logreg)[1] + coef(logreg)[2] * secondclass)
flux.sigmoid(coef(logreg)[1] + coef(logreg)[2] * thirdclass)
flux.predict(logreg, DataFrame(Survived = [1], Pclass = [3]))
glm.predict(logreg)

pred(x) = 1/(1+exp(-(coef(logreg)[1] + coef(logreg)[2]*x)))

xGrid = 0:0.1:maximum(train[:Pclass])

plot(xGrid, pred.(xGrid))

mlogreg = glm(
    @formula(Survived ~ Pclass + Sex + Age + Parch + Fare),
    train,
    Binomial(),
    LogitLink()
)

dataframes.head(train)
size(train) # shape
dataframes.describe(train)

## Outliers
statsplots.boxplot(hcat(train[:Fare], train[:Age]))
# Mean and standard deviation
faremean = statistics.mean(train.Fare)
farestd = statistics.std(train.Fare)

# Look how data is distributed
faredist = glm.fit(distributions.Normal, train[:Fare])
agedist = glm.fit(distributions.Normal, train[:Age])

statsplots.plot(
    distributions.Normal(0, 1),
    title = "Age"
) # Start with normal distribution
statsplots.density!(train[:Age]) # Overlay Age distribution
distributions.skewness(train.Age)

statsplots.plot(
    distributions.Normal(0, 1),
    title = "Fare"
) # Start with normal distribution
statsplots.density!(train[:Fare]) # Overlay Fare distribution
distributions.skewness(train.Fare)

train[[:Title, :Sex, :Mr, :Mrs, :Miss, :Master, :Unknown, :Rev]]

statsplots.plot(
    statsplots.qqplot(
     train[:Fare],
     train[:Age],
     qqline = :fit,
     title = "Fare/Age"
    ), # qqplot of two samples, show a fitted regression line
)

statsplots.plot(
    statsplots.qqplot(
    distributions.Normal(0,1),
     train[:Fare],
     title = "Fare/Normal"
    ), # qqplot of two samples, show a fitted regression line
)

statsplots.plot(
    statsplots.qqplot(
    distributions.Normal(0,1),
     train[:Age],
     title = "Age/Normal"
    ), # qqplot of two samples, show a fitted regression line
)
statsplots.histogram(randn(100), normed=true)

###############################################################################################


#=

Classification Models
---------------------

Logistic Regression
Multiple Logistic Regression
Probit regression
OLS regression
Two-group discriminant function analysis
Hotelling’s T2
Linear Discriminant Analysis
K-nearest neighbours
Generalized Additive Models
Trees
Random Forest
Boosting
Support Vector Machines

=#

# TODO read!! http://ww2.coastal.edu/kingw/statistics/R-tutorials/logistic.html
# TODO analysis on what predictors are most relevent to predicting the response variable
# TODO Research how to count missing values

train = CSV.File("../resource/train.csv") |> dataframes.DataFrame

function titles(xs::Array{Union{Missing, String}, 1})
    acc::Array{String, 1} = []
    for x in xs
        if occursin("Mr.", x)
            push!(acc, "Mr")
        elseif occursin("Mrs.", x)
            push!(acc, "Mrs")
        elseif occursin("Miss.", x)
            push!(acc, "Miss")
        elseif occursin("Master.", x)
            push!(acc, "Master")
        elseif occursin("Rev.", x)
            push!(acc, "Rev")
        else push!(acc, "Unknown")
        end
    end
    acc
end

function encodinglabels(features::Array{String, 1})
    Symbol.(mllabelutils.labelenc(features) |> mllabelutils.label)
end
########################
## Feature Extraction ##
########################

# Data Transformation #

meanage = train[:Age] |> skipmissing |> statistics.mean |> round # Mean Age
train[:Age] = Missings.replace(train[:Age], meanage) |> collect # Replace missing with mean Age

meanfare = train[:Fare] |> skipmissing |> statistics.mean |> round # Mean Fare
train[:Fare] = Missings.replace(train[:Fare], meanfare) |> collect # Replace missing with mean Fare

modeembarked = train.Embarked |> skipmissing |> statsbase.mode # Mode Embarked
train.Embarked = Missings.replace(train.Embarked, modeembarked) |> collect # Replace missing Embarked with mode

train[:Fare] = glm.zscore(train[:Fare]) # Scale Fare for outliers
train[:Age] = glm.zscore(train[:Age]) # Scale Age for outliers

train[:Title] = titles(train[:Name]) # Create new column Title with titles extracted from Name

train[:Sex] = mllabelutils.convertlabel([1, -1], train[:Sex]) # 1 = male, -1 = female

embarkedonehot = mllabelutils.convertlabel(
    mllabelutils.LabelEnc.OneOfK,
    train.Embarked
) |> transpose

embarkedf = dataframes.names!(
    dataframes.DataFrame(embarkedonehot),
    encodinglabels(train.Embarked)
)

titleonehot = mllabelutils.convertlabel(
    mllabelutils.LabelEnc.OneOfK,train[:Title]
) |> transpose # One-hot encode + reshape matrix to allow being converted to data frame

titlesdf = dataframes.names!(
    dataframes.DataFrame(titleonehot),
    encodinglabels(train.Title)
)

titlesdf = dataframes.names!(
    dataframes.DataFrame(titleonehot),
    encodinglabels(train.Title)
)

train = hcat(train, titlesdf, embarkedf) # Concatenate one-hot encoded titles to training DataFrame
dataframes.deletecols!(train, :Name)   # Remove name as we dont need it
dataframes.deletecols!(train, :Cabin)  # Remove cabin as it has a high number of missing values
dataframes.deletecols!(train, :Ticket) # Remove ticket as not enough is known about it

########################
## Exploration        ##
########################

# TODO Go through slides on dimensionality reduction (feature selection)


# Remove any high;y correlated variables

#vDrvp highly correlated features from the model

corrdf = train[
    [
        :Survived,
        :Pclass,
        :Sex,
        :Age,
        :SibSp,
        :Parch,
        :Fare,
        :Mr,
        :Mrs,
        :Miss,
        :Master,
        :Unknown,
        :Rev,
        :S,
        :C,
        :Q
    ]
]

#=
Can you correlate categorical variables?
Without order, it's not possible to correlate two variables. But never fear, there
are ways to find out if categorical variables are related in some way; you need to
simply move from correlation to association. These would be tests such
as Chi square and ANOVAs.
=#

statsplots.crosscor(train.S, train.C)

cormat = convert(
    Array{Float64, 2},
    corrdf
) |> distributions.cor

statsplots.corrplot(cormat, label = names(corrdf))

statsplots.@df corrdf statsplots.boxplot(
    [:Rev :S :C :Q],
    size = (2000, 1500),
)

# TODO read slides on dimensiality redection

#=
Rev+S+C+Q <- TODO find whats up with these.. PCA will fix this but first look
into finding out which variables are the culprits of failing colinearality when model is fit
=#
logreg = glm.glm(
    glm.@formula(
        Survived ~ Pclass+Age+SibSp+Parch+Fare+Sex
    ),
    corrdf,
    glm.Binomial(),
    glm.LogitLink()
)
statsplots.plot(
    statsplots.qqplot(
     train[:Fare],
     train[:Age],
     qqline = :fit,
     title = "Fare/Age"
    ), # qqplot of two samples, show a fitted regression line
)

glm.deviance(logreg)
statsplots.scatter(glm.predict(logreg, train[[:Pclass, :Age, :Sex, :SibSp, :Parch, :Fare]]))

statsplots.scatter([0, 1, 1, 0, 0, 0, 1])

statsplots.histogram(randn(100), normed=true)

onehotbatch(train.Sex, label(train.Sex))


#############################################################


train = File("../resource/train.csv") |> DataFrame

train.Sex = convert(Array{String, 1}, train.Sex)

labels = train[:Survived]
features = train[[:Sex, :Age, :SibSp, :Parch]]

meanage = train.Age |> skipmissing |> mean |> round
features.Age = replace(features.Age, meanage) |> collect
features.Sex = convertlabel([1, -1], features.Sex)

features = Matrix(features) |> transpose

normedfeatures = normalise(features, dims=2)

klasses = unique(labels) |> sort
onehotlabels = onehotbatch(labels, klasses)

# xtrain = splitobs(normedfeatures, at=0.7)[1]
# ytrain = splitobs(onehotlabels, at=0.7)[1]

trainindices = [1:2:891 ; 2:2:891]
xtrain = normedfeatures[:, trainindices]
ytrain = onehotlabels[:, trainindices]

xtest = normedfeatures[:, 3:2:150] # xtest and ytest are the same and
ytest = onehotlabels[:, 3:2:150]


model = Chain(
    Dense(4, 2),# xrows, yrows
    softmax
)

loss(x, y) = crossentropy(model(x), y)
optimiser = Descent(0.5)
dataiterator = Iterators.repeated((xtrain, ytrain), 110)

train!(loss, params(model), dataiterator, optimiser)
accuracy(x, y) = mean(onecold(model(x)) .== onecold(y))

score = accuracy(xtest, ytest)

@assert score > 0.8

function confusionmatrix(X, y)
    ŷ = onehotbatch(onecold(model(X)), 1:3)
    y * ŷ'
end

cmat = confusionmatrix(xtest, ytest)

display(cmat)



survived = File("../resource/titanic/gender_submission.csv") |> DataFrame

train = File("../resource/titanic/train.csv") |> DataFrame
test = File("../resource/titanic/test.csv") |> DataFrame

test = hcat(test, survived.Survived)

rename!(test, :x1, :Survived)

describe(test)

test[:Survived]

test = test[[:PassengerId, :Survived, :Pclass, :Name, :Sex, :Age, :SibSp, :Parch]]

out = vcat(test, train)

write("/Users/greade01/data.csv", out, delim=",")

train = File("../resource/titanic/train.csv") |> DataFrame
test = File("../resource/titanic/test.csv") |> DataFrame
sur = File("../resource/titanic/gender_submission.csv") |> DataFrame

test = hcat(sur[:Survived], test)
rename!(test, :x1, :Survived)

data = vcat(train, test)
describe(data)
write("/Users/greade01/data.csv", data)

#################################################################################################
# DIGIT RECOGNISER
#################################################################################################

function renamefiles(path::String)
    filenames = readdir(path)
    for (index, filename) in enumerate(filenames)
        fpsource = string(path, filename)
        fpdestination = string(path, index, ".jpg")
        mv(fpsource, fpdestination)
    end
end

function loadimages(path::String)::Array{Tuple{Label, GreyImage}}
    images::Array{GreyImage, 1} = []
    labels::Array{Label, 1} = []
    nothidden = file -> !startswith(file, ".")
    files = filter(nothidden, readdir(path))
    for file in files
        imagepath = string(path, "/", file)
        image = load(imagepath)
        label = split(file, ".") |> first |> last |> parseint
        push!(labels, label)
        push!(images, image)
    end
    zip(labels, images) |> collect
end

function minibatch(batch::MiniBatch)
    indexes = partition(1:length(batch.features.images), batch.size)
    indexeslength = 1:length(indexes)
    xbatch = Array{Float32}(
        undef,
        size(batch.features.images[1])...,
        1,
        indexeslength
    )
    for index in indexeslength
        xbatch[:, :, :, index] = Float32.(batch.features.images[indexes[index]])
    end
    return xbatch
end


parse(Int64, "22")




#########################################################



import Flux: Data.MNIST, Dense, Conv, Chain, leakyrelu, softmax, softmax, onehotbatch, OneHotMatrix, OneHotVector, relu, maxpool
import Images: load, channelview, ColorTypes, FixedPointNumbers
import FileIO: load
import Base.Iterators: partition
import MLDataUtils: batchview
import Base: Fix1
import StatsPlots: plot, plotlyjs, plot, heatmap, density
import Base: Iterators.PartitionIterator
import Transducers
import MLDataUtils: splitobs

plotlyjs()

parseint = Fix1(parse, Int)

GreyImage = Array{ColorTypes.Gray{FixedPointNumbers.Normed{UInt8,8}}, 2}
Images = Array{GreyImage, 1}
MiniBatchIndex = Array{Int64, 1}
Label = Int64
Labels = Array{Label, 1}
MiniBatchedImages = Array{Float32, 4}
MiniBatchedLabels = OneHotMatrix{Array{OneHotVector,1}}
ZippedImageFeatures = Base.Iterators.Zip

# struct ImageFeatures
#     labels::Labels
#     images::Images
# end

function loadimages(path::String)::ZippedImageFeatures
    images::Array{GreyImage, 1} = []
    labels::Array{Label, 1}  = []
    nothidden = file -> !startswith(file, ".")
    files = filter(nothidden, readdir(path))
    for file in files
        imagepath = string(path, "/", file)
        image = load(imagepath)
        label = split(file, ".") |> first |> last |> parseint
        push!(labels, label)
        push!(images, image)
    end
    zip(labels, images) 
    # ImageFeatures(labels, images)
end

@info("loading dataset")
collectedfeatures = loadimages("./resource/digitrecogniser/data") |> collect

train, test = splitobs(collectedfeatures, at=.7, obsdim=ndims(collectedfeatures))


function minibatch(batchindex::MiniBatchIndex, images::Images)::MiniBatchedImages
    batch = Array{Float32}(
        undef,
        size(images[1])...,
        1,
        length(batchindex)
    )
    for index in 1:length(batchindex)
        batch[:, :, :, index] = Float32.(images[batchindex[index]])
    end
    batch
end

function minibatch(batchindex::MiniBatchIndex, labels::Labels)::MiniBatchedLabels
    batch = onehotbatch(labels[batchindex], 0:9)
    batch
end


# indexes = partition(1:length(features.images), 128)

trainindexes = partition(1:length(train), 128)


trainimages = [minibatch(index, features.images) for index in indexes]


trainlabels = [minibatch(index, features.labels) for index in indexes]

model = Chain(
    # First convolution, operating upon a 28x28 image
    Conv((3, 3), 1=>12, pad=(1, 1), relu),
    x -> maxpool(x, (2, 2)),

    # Second convolution, operating upon a 14x14 image
    Conv((3, 3), 16=>32, pad=(1, 1), relu),
    x -> maxpool(x, (2, 2)),

    # Third convolution, operating upon a 7x7 image
    Conv((3, 3), 32=>32, pad=(1, 1), relu),
    x -> maxpool(x, (2, 2)),

    # Reshape 3d tensor into a 2d one, at this point it should be (3, 3, 32, N)
    # which is where we get the 288 in the `Dense` layer below:
    x -> reshape(x, :, size(x, 4)),
    Dense(288, 10),

    # Finally, softmax to get nice probabilities
    softmax
)

# @assert score > 0.8


train!()

trainimages

size(trainimages)

28*28


NamedTuple{(:a, :b),Tuple{Int64, Int64}}.(zip([3, 4], [1, 2]))