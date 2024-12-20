import csv
import itertools
import sys

PROBS = {

    # Unconditional probabilities for having gene
    "gene": {
        2: 0.01,
        1: 0.03,
        0: 0.96
    },

    "trait": {

        # Probability of trait given two copies of gene
        2: {
            True: 0.65,
            False: 0.35
        },

        # Probability of trait given one copy of gene
        1: {
            True: 0.56,
            False: 0.44
        },

        # Probability of trait given no gene
        0: {
            True: 0.01,
            False: 0.99
        }
    },

    # Mutation probability
    "mutation": 0.01
}


def main():

    # Check for proper usage
    if len(sys.argv) != 2:
        sys.exit("Usage: python heredity.py data.csv")
    people = load_data(sys.argv[1])

    # Keep track of gene and trait probabilities for each person
    probabilities = {
        person: {
            "gene": {
                2: 0,
                1: 0,
                0: 0
            },
            "trait": {
                True: 0,
                False: 0
            }
        }
        for person in people
    }

    # Loop over all sets of people who might have the trait
    names = set(people)
    for have_trait in powerset(names):

        # Check if current set of people violates known information
        fails_evidence = any(
            (people[person]["trait"] is not None and
             people[person]["trait"] != (person in have_trait))
            for person in names
        )
        if fails_evidence:
            continue

        # Loop over all sets of people who might have the gene
        for one_gene in powerset(names):
            for two_genes in powerset(names - one_gene):

                # Update probabilities with new joint probability
                p = joint_probability(people, one_gene, two_genes, have_trait)
                update(probabilities, one_gene, two_genes, have_trait, p)

    # Ensure probabilities sum to 1
    normalize(probabilities)

    # Print results
    for person in people:
        print(f"{person}:")
        for field in probabilities[person]:
            print(f"  {field.capitalize()}:")
            for value in probabilities[person][field]:
                p = probabilities[person][field][value]
                print(f"    {value}: {p:.4f}")


def load_data(filename):
    """
    Load gene and trait data from a file into a dictionary.
    File assumed to be a CSV containing fields name, mother, father, trait.
    mother, father must both be blank, or both be valid names in the CSV.
    trait should be 0 or 1 if trait is known, blank otherwise.
    """
    data = dict()
    with open(filename) as f:
        reader = csv.DictReader(f)
        for row in reader:
            name = row["name"]
            data[name] = {
                "name": name,
                "mother": row["mother"] or None,
                "father": row["father"] or None,
                "trait": (True if row["trait"] == "1" else
                          False if row["trait"] == "0" else None)
            }
    return data


def powerset(s):
    """
    Return a list of all possible subsets of set s.
    """
    s = list(s)
    return [
        set(s) for s in itertools.chain.from_iterable(
            itertools.combinations(s, r) for r in range(len(s) + 1)
        )
    ]


def check_how_many_copies(person, one_gene, two_genes):
    if person in two_genes:
        return 2
    elif person in one_gene:
        return 1
    else:
        return 0


def probs_no_parents(copies_gene, has_trait):
    """
    Calculate the probability of a person having `copies_gene` genes and the trait `has_trait`
    when the person has no known parents.
    """
    gene_prob = PROBS["gene"][copies_gene]
    trait_prob = PROBS["trait"][copies_gene][has_trait]
    return gene_prob * trait_prob


def probs_has_parents(person, people, one_gene, two_genes, has_trait):
    """
    Calculate the probability of a person having `copies_gene` genes and the trait `has_trait`,
    considering the gene probabilities of the parents.
    """
    # Get the mother's and father's gene counts
    mother = people[person]["mother"]
    father = people[person]["father"]

    if mother is None or father is None:
        return probs_no_parents(check_how_many_copies(person, one_gene, two_genes), has_trait)

    # Parent gene counts
    mother_genes = check_how_many_copies(mother, one_gene, two_genes)
    father_genes = check_how_many_copies(father, one_gene, two_genes)

    # Calculate probabilities of inheritance
    prob_mother_pass_gene = mother_genes / 2
    prob_father_pass_gene = father_genes / 2

    # Mutation probability
    p_mutate = PROBS["mutation"]
    p_no_mutate = 1 - p_mutate

    # Child gene possibilities
    prob_father_pass_gene_and_dont_mutate = prob_father_pass_gene * p_no_mutate
    prob_father_pass_gene_and_do_mutate = prob_father_pass_gene * p_mutate
    prob_father_dont_pass_gene_and_dont_mutate = (1 - prob_father_pass_gene) * p_no_mutate
    prob_father_dont_pass_gene_and_do_mutate = (1 - prob_father_pass_gene) * p_mutate

    prob_mother_pass_gene_and_dont_mutate = prob_mother_pass_gene * p_no_mutate
    prob_mother_pass_gene_and_do_mutate = prob_mother_pass_gene * p_mutate
    prob_mother_dont_pass_gene_and_dont_mutate = (1 - prob_mother_pass_gene) * p_no_mutate
    prob_mother_dont_pass_gene_and_do_mutate = (1 - prob_mother_pass_gene) * p_mutate

    # Calculate the total probability for the child
    child_genes = check_how_many_copies(person, one_gene, two_genes)
    probability = 0

    # Calculate probability based on how many genes the child has
    if child_genes == 0:
        probability += (prob_father_dont_pass_gene_and_dont_mutate * prob_mother_dont_pass_gene_and_dont_mutate) + \
                       (prob_father_dont_pass_gene_and_do_mutate * prob_mother_dont_pass_gene_and_do_mutate) + \
                       (prob_father_pass_gene_and_dont_mutate * prob_mother_pass_gene_and_dont_mutate) + \
                       (prob_father_pass_gene_and_do_mutate * prob_mother_pass_gene_and_do_mutate)
    elif child_genes == 1:
        probability += (prob_father_pass_gene_and_dont_mutate * prob_mother_dont_pass_gene_and_dont_mutate) + \
                       (prob_father_dont_pass_gene_and_dont_mutate * prob_mother_pass_gene_and_dont_mutate) + \
                       (prob_father_dont_pass_gene_and_do_mutate * prob_mother_pass_gene_and_do_mutate) + \
                       (prob_father_pass_gene_and_do_mutate * prob_mother_dont_pass_gene_and_do_mutate)
    elif child_genes == 2:
        probability += (prob_father_pass_gene_and_dont_mutate * prob_mother_pass_gene_and_dont_mutate) + \
                       (prob_father_dont_pass_gene_and_do_mutate * prob_mother_dont_pass_gene_and_do_mutate) + \
                       (prob_father_dont_pass_gene_and_do_mutate * prob_mother_pass_gene_and_dont_mutate) + \
                       (prob_father_pass_gene_and_dont_mutate *
                        prob_mother_dont_pass_gene_and_do_mutate)

    return probability * PROBS["trait"][child_genes][has_trait]


def joint_probability(people, one_gene, two_genes, have_trait):
    """
    Compute and return a joint probability.

    The probability returned should be the probability that
        * everyone in set `one_gene` has one copy of the gene, and
        * everyone in set `two_genes` has two copies of the gene, and
        * everyone not in `one_gene` or `two_gene` does not have the gene, and
        * everyone in set `have_trait` has the trait, and
        * everyone not in set `have_trait` does not have the trait.
    """
    probability = 1

    for person in people:
        # Check how many gene copies this person has
        copies_gene = check_how_many_copies(person, one_gene, two_genes)
        has_trait = person in have_trait  # Whether the person has the trait

        # If the person has no known parents, use the unconditional probabilities
        if people[person]["mother"] is None or people[person]["father"] is None:
            # Use the no-parents case to calculate the gene and trait probability
            probability *= probs_no_parents(copies_gene, has_trait)

        else:
            # Otherwise, calculate probability considering the parents' gene distributions
            probability *= probs_has_parents(person, people, one_gene, two_genes, has_trait)

    return probability


def update(probabilities, one_gene, two_genes, have_trait, p):
    """
    Add to `probabilities` a new joint probability `p`.
    Each person should have their "gene" and "trait" distributions updated.
    Which value for each distribution is updated depends on whether
    the person is in `have_gene` and `have_trait`, respectively.
    """
    for person in probabilities:
        copies_gene = check_how_many_copies(person, one_gene, two_genes)

        has_trait = person in have_trait

        probabilities[person]["gene"][copies_gene] += p
        probabilities[person]["trait"][has_trait] += p


def normalize(probabilities):
    """
    Update `probabilities` such that each probability distribution
    is normalized (i.e., sums to 1, with relative proportions the same).
    """
    for person in probabilities:
        for field in ["gene", "trait"]:
            total = sum(probabilities[person][field].values())
            for key in probabilities[person][field]:
                probabilities[person][field][key] /= total


if __name__ == "__main__":
    main()
